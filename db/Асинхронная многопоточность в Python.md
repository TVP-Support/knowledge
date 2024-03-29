---
tags: [python]
---
# Асинхронная многопоточность в Python

## Вступление

В этом разделе я попытаюсь сформулировать цель своей статьи (можете пропустить его, если хотите просто узнать, как настроить асинхронную многопоточность в Python). Мне потребовалось много времени, чтобы методом проб и ошибок освоить и научиться применять параллельный код. В этом мне помогли StackOverflow, TDS и т. д., а также отличные руководства для изучения основ параллельного программирования.

И все же должен признать: едва вы приступите к распараллеливанию, как перед вами встанет множество проблем при вводе массы данных и осуществлении различных операций. К примеру, проблема, над которой я работал, требовала 15 миллионов вызовов API, которые сопровождались проблемами масштабируемости. Я не эксперт в параллельном программировании, просто хотел бы поделиться тем, что сам узнал, и теми сложностями, с которыми столкнулся. Надеюсь, мое руководство будет полезным для других новичков, таких как я!

## Асинхронность против многопоточности

На первых порах я путался в этих понятиях, поэтому, думаю, не лишним будет на них остановиться. Асинхронное программирование обычно используется в случаях, когда определенное действие выполняется медленно, блокируя выполнение следующих. Вы часто видите это при вводе-выводе, например при сетевых вызовах, когда возврат одного из них может занять сотни миллисекунд.

Когда программа ждет ответа на вызов, вы бездействуете и тратите драгоценное время. Выполняя вызовы асинхронно, вы можете немедленно отправлять их один за другим и перехватывать позже с помощью функции обратного вызова. Это что-то вроде питчера, способного бросить сразу несколько мячей вместо одного.

С другой стороны, многопоточность приводит к появлению большего количества _потоков_, каждый из которых может выполнять действия параллельно. Поэтому, если асинхронное программирование — это питчер с несколькими мячами, то многопоточность — игра с целой командой питчеров. [Здесь](https://stackoverflow.com/questions/34680985/what-is-the-difference-between-asynchronous-programming-and-multithreading#:~:text=In%20multithreaded%20workflows%20you%20assign,of%20the%20just%2Dcompleted%20task.) можно найти более подробное объяснение.

## Как быстро это происходит?

Очень быстро. При решении моей задачи каждый вызов API занимал около 100 миллисекунд. При последовательном выполнении я мог получать около 10 вызовов в секунду. Чтобы получить 15 миллионов вызовов, мне пришлось бы потратить примерно 3 недели.

С помощью параллельного программирования я смог выполнить более 2500 вызовов в секунду, получив все 15 миллионов всего за 2 часа (такого ускорения я добился, запустив модуль Kubernetes, но ваши результаты могут немного отличаться в зависимости от типа машины, лимита памяти и возможностей процессора).

## Настройка

Допустим, у нас есть медленная функция, на выполнение которой требуется 100 миллисекунд.

```python
def slow_function(_data_):
    time.sleep(100/1000)
    return data
```

Мы хотим запустить эту функцию много раз и экспортировать результат:

```python
if __name__ == "__main__":
    output = []
    for i in range(10000):
        output.append(slow_function(i))

write_to_file(output)
```

10000 последовательных вызовов займут не менее 1000 секунд (100 м/с на вызов для 10000 вызовов) или около 16 минут.

## Распараллеливание

Для распараллеливания мы можем использовать встроенную в Python библиотеку `multiprocessing`:

```python
import multiprocessing as mp
```

Сначала мы должны написать функцию обратного вызова, которая сообщит программе, что делать, как только функция `slow_function` выполнит возврат. Она принимает переменную `result`, которая является результатом функции `slow_function`, и производит с ней определенные действия. В этом случае мы просто добавим ее в список:

```python
def catch(result):
    global output
    output.append(result)
```

Наконец, мы настраиваем пул потоков и вызываем функцию асинхронно:

```python
JOBS = 200 # 1

if __name__ == "__main__":
    mp.set_start_method("spawn") # 2
    pool = mp.Pool(JOBS) # 3

output = []
    for i in range(10000):
        pool.apply_async(slow_function, args=(i, ), \
            callback=catch) # 4 
            
pool.close() # 5
pool.join()

write_to_file(output)
```

1. Переменная `JOBS` определяет, сколько потоков вы хотите создать. Чем больше вы порождаете их, тем быстрее будет выполняться программа. Однако слишком большое количество может создать излишнюю нагрузку на процессор или переполнение памяти. Поэкспериментируйте, пока не найдете оптимальный вариант (я пробовал от 4 до 200 с разным количеством, подходящим для разных машин).
2. Установите для метода запуска значение "порождение", чтобы избежать этого [проблемного момента](https://pythonspeed.com/articles/python-multiprocessing/).
3. Настройте пул потоков с выбранным количеством потоков.
4. Вызовите функцию `slow_function` с помощью `pool.apply_async()` при передаче функции обратного вызова.
5. Используйте `pool.close()`, чтобы запретить потокам принимать новую работу, и `pool.join()`, чтобы завершить потоки (более подробную информацию см. на этом [форуме](https://stackoverflow.com/questions/38271547/when-should-we-call-multiprocessing-pool-join)).

**Примечание: из-за параллельного характера программы выходные данные не будут упорядочены. В этом есть свой плюс: вы можете возвращать входные данные с каждым выводом, чтобы сопоставлять их по мере необходимости.

Если вы запустите этот код, он будет хорошо работать для входных данных среднего размера (~10-100 тыс. действий). Однако, если входные данные намного больше, что потребует миллионы операций, то вы можете столкнуться с двумя основными проблемами:

- из-за значительного ускорения работы может произойти перегрузка процессора;
- выходные данные могут быть настолько большими, что переполнят память.

## Решение для процессора

Чтобы устранить проблему с процессором, я запускал все по частям, с небольшими интервалами между каждым блоком:

```python
JOBS = 200
CHUNKSIZE = 1000

def process_chunk(chunk, pool):
    for data in chunk:
        pool.apply_async(slow_function, args=(data, ),
                         callback=catch)
if __name__ == "__main__":
    mp.set_start_method("spawn")
    pool = mp.Pool(JOBS)

output = []
chunk = []
for i in range(10000):
    chunk.append(i)
    
    if (i+1) % CHUNKSIZE == 0:
        process_chunk(chunk, pool)
        chunk = []
        time.sleep(500/1000)
        
pool.close()
pool.join()

write_to_file(output)
```

Вы также можете уменьшить количество заданий, чтобы уменьшить нагрузку на процессор.

## Решение для памяти

Чтобы решить проблему с памятью, я экспортировал выходные данные пакетами (каждые несколько блоков) и очистил хранилище данных в памяти (а также делал более длительные интервалы между пакетами):

```python
JOBS = 200
CHUNKSIZE = 1000
BATCHSIZE = 5


def process_chunk(chunk, pool):
    for data in chunk:
        pool.apply_async(slow_function, args=(data, ),
                         callback=catch)


if __name__ == "__main__":
    mp.set_start_method("spawn")
    pool = mp.Pool(JOBS)

output = []
chunk = []
for i in range(10000):
    chunk.append(i)

    if (i+1) % CHUNKSIZE == 0:
        process_chunk(chunk, pool)
        chunk = []
        time.sleep(500/1000)

        if (i+1) % (BATCHSIZE * CHUNKSIZE) == 0:
            write_to_file(output)
            output = []

pool.close()
pool.join()
```

## Полный код

Вот полный распараллеленный код, включающий в себя решения проблем с процессором и памятью.

```python
import time
import timeit
import multiprocessing as mp

# set parameters here
JOBS = 200
CHUNKSIZE = 1000
BATCHSIZE = 5

def slow_function(data):
  time.sleep(100/1000)
  return data

def catch(result):
    global output
    output.append(result)

def process_chunk(chunk, pool):
  for data in chunk:
    pool.apply_async(slow_function, args=(data, ), \
      callback=catch)

if __name__ == "__main__":
  mp.set_start_method("spawn")
  pool = mp.Pool(JOBS)

  start = timeit.default_timer()

  output = []
  chunk = []
  for i in range(10000):
    chunk.append(i)

    if (i+1) % CHUNKSIZE == 0:        
      process_chunk(chunk, pool)
      chunk = []
      time.sleep(500/1000)

    if (i+1) % (BATCHSIZE * CHUNKSIZE) == 0:
      write_to_file(output)
      output = []

  pool.close()
  pool.join()

  stop = timeit.default_timer()
  print("##### done in ", stop - start, " seconds #####")
```

Я настроил указанные выше параметры как глобальные, в том числе количество потоков, размер блока и объем пакета. Поэкспериментируйте с ними, чтобы получить оптимальную производительность для своей машины и входных данных.

Моя производительность достигла порядка 10 000 000+ вызовов. При более высоких значениях может возникнуть больше проблем и трудностей, требующих поиска решений.

## Контрольное время

Ранее я подсчитал, что для последовательного запуска этого примера программы потребуется не менее 1000 секунд. При использовании приведенного выше кода, работающего локально на стандартном 8-ядерном Macbook Pro 2019 года, я оценил эффективность работы в ~11 секунд, что примерно в 100 раз быстрее.

## Ссылки

- [Источник](https://medium.com/nuances-of-programming/%D0%B0%D1%81%D0%B8%D0%BD%D1%85%D1%80%D0%BE%D0%BD%D0%BD%D0%B0%D1%8F-%D0%BC%D0%BD%D0%BE%D0%B3%D0%BE%D0%BF%D0%BE%D1%82%D0%BE%D1%87%D0%BD%D0%BE%D1%81%D1%82%D1%8C-%D0%B2-python-7920b53f2752)
- [Оригинал статьи](https://maxliuofficial.medium.com/asynchronous-multithreading-in-python-5278bed9ed44)
- [Python](Python.md)
