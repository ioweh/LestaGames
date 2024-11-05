# Lesta Games
Тестовое задание

Вопрос №1

На языке Python написать алгоритм (функцию) определения четности целого числа, который будет аналогичен нижеприведенному по функциональности, но отличен по своей сути. Объяснить плюсы и минусы обеих реализаций. 

Пример: 

```python
def isEven(value):

      return value % 2 == 0
```




Сразу получилось написать вот такой код:

```python
def isEvenBwise(value):
    return value == ((value >> 1) << 1)
```

Плюсы могу объяснить цитатой из книжки Thinking Forth:

https://www.forth.com/starting-forth/7-signed-double-length-numbers/

"The important thing for you to know is that most computers can shift digits much more quickly than they can go through all the folderol of normal division or multiplication."

Второй пример будет работать быстрее, в то время как первый более легко прочесть.

Могу третий пример добавить, с побитовым сложением:

```python
return not (value & 1)
```

единичка всегда будет выглядеть в двоичной записи как ...000001

а любое нечетное число - это четное, к которому добавлена единичка

поэтому если их побитово сложить и принять во внимание что все числа при сложении будут нулями, кроме последнего, в силу двоичного представления единички, сразу определим четное число или нет

----------------------------------

Вопрос №2

На языке Python написать минимум по 2 класса реализовывающих циклический буфер FIFO. Объяснить плюсы и минусы каждой реализации.

Оценивается:

    Полнота и качество реализации
    Оформление кода
    Наличие сравнения и пояснения по быстродействию


Первая реализация:

```python
class queue:
    q = []

    def push(self, value):
        self.q.append(value)

    def pop(self):
        if self.q:
            return self.q.pop(0)
        else:
            return None

    def print(self):
        print(self.q[::-1])
```


Использовал push и pop для помещения и извлечения элементов, добавил print для вывода на экран.

По производительности, добавление в очередь занимает O(1), удаление O(n).

Добавление в конец списка произойдет за один шаг. А вот чтобы убрать первый элемент, весь список нужно будет сдвинуть поэлементно.

Можно попробовать убрать улучшить производительность используя doubly linked list.


Вторая реализация:

```python
from collections import deque

class queue:
    q = deque()

    def push(self, value):
        self.q.append(value)

    def pop(self):
        if self.q:
            return self.q.popleft()
        else:
            return None

    def print(self):
        print(list(self.q)[::-1])
```

Здесь, в силу того, что используем двусвязный список, удаление элемента не вызовет поэлементного сдвига всего списка, а следовательно выполнится за O(1).

----------------------------------

Вопрос №3

На языке Python предложить алгоритм, который быстрее всего (по процессорным тикам) отсортирует данный ей массив чисел. Массив может быть любого размера со случайным порядком чисел (в том числе и отсортированным). Объяснить, почему вы считаете, что функция соответствует заданным критериям.


Быстрая сортировка:

```python
def sort(array):
    # base case: arrays of length 0 or 1 are already sorted
    if len(array) <= 1:
        return array

    # choose the pivot
    pivot = array[len(array) >> 1]

    # exclude the pivot
    smaller = [x for x in array if x < pivot]
    equal = [x for x in array if x == pivot]
    larger = [x for x in array if x > pivot]

    # recursively sort and combine results
    return sort(smaller) + equal + sort(larger)
```


Почему способ неоптимален: упоминается что массив может быть отсортирован.

Из Кормена, Лейзерсона:

Moreover, the O(n^2) running time occurs when the input array is already completely sorted... (7.2. Performance of quicksort).

Там же можно посмотреть, что у merge sort worst case равен O(n log n). Поэтому в данном случае, когда нас интересует скорость выполнения на любых данных, лучше взять merge sort.

```python
def merge_sort(array):
    # base case: arrays with 2 or less elements are already sorted
    if len(array) <= 1:
        return array

    mid = len(array) // 2
    left = array[:mid]
    right = array[mid:]

    left_sorted = merge_sort(left)
    right_sorted = merge_sort(right)

    return merge(left_sorted, right_sorted)

def merge(left, right):
    sorted_array = []
    i = j = 0

    # compare elements one by one
    while i < len(left) and j < len(right):
        if left[i] < right[j]:
            sorted_array.append(left[i])
            i += 1
        else:
            sorted_array.append(right[j])
            j += 1

    # remaining elements from the left half
    sorted_array.extend(left[i:])

    # remaining elements from the right half
    sorted_array.extend(right[j:])

    return sorted_array
```

test = [1,4,2,1,8,3]
merge_sort(test)
[1, 1, 2, 3, 4, 8]

