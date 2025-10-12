---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.17.3
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

# **Линейные списки (Linked list)**


## **Цель работы**


изучение структуры данных «Линейные списки», а также основных операций над ними.


### Задание 1

```python
from typing import Any, Optional, TypeVar

ValueType = TypeVar('ValueType')

class Node:
    """Узел односвязного списка"""
    def __init__(self, value: ValueType):
        self.value = value
        self.next: Optional['Node'] = None

    def __str__(self):
        return f"Node({self.value})"


# ===== ВЕРСИЯ 1 =====
class SingleLinkedListV1:
    """Базовая версия односвязного списка"""
    
    def __init__(self):
        """Возвращает пустой односвязный список"""
        self.head: Optional[Node] = None
    
    def insert_first_node(self, value: ValueType) -> None:
        """Добавить узел в начало списка"""
        new_node = Node(value)
        new_node.next = self.head
        self.head = new_node
    
    def remove_first_node(self) -> ValueType:
        """Удалить первый узел списка и вернуть его значение"""
        if self.head is None:
            raise IndexError("Cannot remove from empty list")
        
        value = self.head.value
        self.head = self.head.next
        return value
    
    def insert_last_node(self, value: ValueType) -> None:
        """Добавить узел в конец списка"""
        new_node = Node(value)
        
        if self.head is None:
            self.head = new_node
            return
        
        current = self.head
        while current.next is not None:
            current = current.next
        current.next = new_node
    
    def remove_last_node(self) -> ValueType:
        """Удалить последний элемент списка"""
        if self.head is None:
            raise IndexError("Cannot remove from empty list")
        
        # Если в списке только один элемент
        if self.head.next is None:
            value = self.head.value
            self.head = None
            return value
        
        # Ищем предпоследний элемент
        current = self.head
        while current.next.next is not None:
            current = current.next
        
        value = current.next.value
        current.next = None
        return value
    
    def __str__(self):
        values = []
        current = self.head
        while current:
            values.append(str(current.value))
            current = current.next
        return " -> ".join(values) if values else "Empty list"


# ===== ВЕРСИЯ 2 =====
class SingleLinkedListV2(SingleLinkedListV1):
    """Добавлены методы поиска и замены"""
    
    def __init__(self):
        super().__init__()
        self._size = 0
    
    def get_size(self) -> int:
        """Вернуть длину списка"""
        return self._size
    
    def insert_first_node(self, value: ValueType) -> None:
        super().insert_first_node(value)
        self._size += 1
    
    def remove_first_node(self) -> ValueType:
        value = super().remove_first_node()
        self._size -= 1
        return value
    
    def insert_last_node(self, value: ValueType) -> None:
        super().insert_last_node(value)
        self._size += 1
    
    def remove_last_node(self) -> ValueType:
        value = super().remove_last_node()
        self._size -= 1
        return value
    
    def find_node(self, value: ValueType) -> Optional[ValueType]:
        """Найти (первый) узел по его значению и вернуть значение"""
        current = self.head
        while current:
            if current.value == value:
                return current.value
            current = current.next
        return None
    
    def replace_node(self, old_value: ValueType, new_value: ValueType) -> None:
        """Найти (первый) узел по его значению и заменить его значение новым"""
        current = self.head
        while current:
            if current.value == old_value:
                current.value = new_value
                return
            current = current.next
    
    def remove_node(self, value: ValueType) -> ValueType:
        """Найти (первый) узел по его значению и удалить его"""
        if self.head is None:
            raise ValueError("Value not found in list")
        
        # Если удаляем голову
        if self.head.value == value:
            return self.remove_first_node()
        
        # Ищем узел для удаления
        current = self.head
        while current.next and current.next.value != value:
            current = current.next
        
        if current.next is None:
            raise ValueError("Value not found in list")
        
        value_to_return = current.next.value
        current.next = current.next.next
        self._size -= 1
        return value_to_return


# ===== ВЕРСИЯ 3 =====
class SingleLinkedListV3(SingleLinkedListV2):
    """Оптимизированная версия с O(1) для get_size"""
    
    def __init__(self):
        super().__init__()
        # Размер уже хранится в _size и обновляется при операциях


# ===== ВЕРСИЯ 4 =====
class SingleLinkedListV4(SingleLinkedListV3):
    """Добавлены методы для работы с соседними узлами"""
    
    def _find_node_object(self, value: ValueType) -> Optional[Node]:
        """Внутренний метод для поиска узла-объекта"""
        current = self.head
        while current:
            if current.value == value:
                return current
            current = current.next
        return None
    
    def find_previos_node(self, value: ValueType) -> Optional[ValueType]:
        """Найти значение из предыдущего узла"""
        if self.head is None or self.head.value == value:
            return None  # У первого элемента нет предыдущего
        
        current = self.head
        while current.next:
            if current.next.value == value:
                return current.value
            current = current.next
        return None
    
    def find_next_node(self, value: ValueType) -> Optional[ValueType]:
        """Найти значение из следующего узла"""
        node = self._find_node_object(value)
        if node and node.next:
            return node.next.value
        return None
    
    def insert_before_node(self, target_value: ValueType, new_value: ValueType) -> None:
        """Добавить узел перед узлом с заданным значением"""
        if self.head is None:
            return
        
        # Если вставляем перед головой
        if self.head.value == target_value:
            self.insert_first_node(new_value)
            return
        
        # Ищем узел, предшествующий целевому
        current = self.head
        while current.next and current.next.value != target_value:
            current = current.next
        
        if current.next is None:  # Узел не найден
            return
        
        new_node = Node(new_value)
        new_node.next = current.next
        current.next = new_node
        self._size += 1
    
    def insert_after_node(self, target_value: ValueType, new_value: ValueType) -> None:
        """Добавить узел после узла с заданным значением"""
        target_node = self._find_node_object(target_value)
        if target_node is None:
            return
        
        new_node = Node(new_value)
        new_node.next = target_node.next
        target_node.next = new_node
        self._size += 1
    
    def replace_previos_node(self, target_value: ValueType, new_value: ValueType) -> None:
        """Заменить значение в предыдущем узле"""
        if self.head is None or self.head.value == target_value:
            return  # У первого элемента нет предыдущего
        
        current = self.head
        while current.next:
            if current.next.value == target_value:
                current.value = new_value
                return
            current = current.next
    
    def replace_next_node(self, target_value: ValueType, new_value: ValueType) -> None:
        """Заменить значение в следующем узле"""
        target_node = self._find_node_object(target_value)
        if target_node and target_node.next:
            target_node.next.value = new_value
    
    def remove_previos_node(self, target_value: ValueType) -> Optional[ValueType]:
        """Удалить предыдущий узел"""
        if self.head is None or self.head.value == target_value:
            return None  # У первого элемента нет предыдущего
        
        # Если удаляем элемент перед головой (невозможно по логике)
        if self.head.next and self.head.next.value == target_value:
            return self.remove_first_node()
        
        # Ищем узел, который находится за два узла до целевого
        current = self.head
        while current.next and current.next.next:
            if current.next.next.value == target_value:
                value_to_return = current.next.value
                current.next = current.next.next
                self._size -= 1
                return value_to_return
            current = current.next
        
        return None
    
    def remove_next_node(self, target_value: ValueType) -> Optional[ValueType]:
        """Удалить следующий узел"""
        target_node = self._find_node_object(target_value)
        if target_node and target_node.next:
            value_to_return = target_node.next.value
            target_node.next = target_node.next.next
            self._size -= 1
            return value_to_return
        return None


# ===== ВЕРСИЯ 5 =====
class SingleLinkedListV5(SingleLinkedListV4):
    """Версия с ссылкой на последний элемент для оптимизации"""
    
    def __init__(self):
        super().__init__()
        self.tail: Optional[Node] = None
    
    def insert_first_node(self, value: ValueType) -> None:
        new_node = Node(value)
        new_node.next = self.head
        self.head = new_node
        
        if self.tail is None:  # Если список был пуст
            self.tail = new_node
        
        self._size += 1
    
    def remove_first_node(self) -> ValueType:
        if self.head is None:
            raise IndexError("Cannot remove from empty list")
        
        value = self.head.value
        self.head = self.head.next
        
        if self.head is None:  # Если список стал пуст
            self.tail = None
        
        self._size -= 1
        return value
    
    def insert_last_node(self, value: ValueType) -> None:
        new_node = Node(value)
        
        if self.tail is None:  # Пустой список
            self.head = new_node
            self.tail = new_node
        else:
            self.tail.next = new_node
            self.tail = new_node
        
        self._size += 1
    
    def remove_last_node(self) -> ValueType:
        if self.head is None:
            raise IndexError("Cannot remove from empty list")
        
        # Если в списке только один элемент
        if self.head == self.tail:
            value = self.head.value
            self.head = None
            self.tail = None
            self._size -= 1
            return value
        
        # Ищем предпоследний элемент
        current = self.head
        while current.next != self.tail:
            current = current.next
        
        value = self.tail.value
        current.next = None
        self.tail = current
        self._size -= 1
        return value


# ===== ВЕРСИЯ 6 =====
class SingleLinkedListV6(SingleLinkedListV5):
    """Версия с оптимизацией вставки перед узлом"""
    
    def insert_before_node(self, target_value: ValueType, new_value: ValueType) -> None:
        """Оптимизированная вставка перед узлом"""
        if self.head is None:
            return
        
        # Если вставляем перед головой
        if self.head.value == target_value:
            self.insert_first_node(new_value)
            return
        
        # Ищем узел, предшествующий целевому
        current = self.head
        while current.next and current.next.value != target_value:
            current = current.next
        
        if current.next is None:  # Узел не найден
            return
        
        # Создаем новый узел и вставляем его
        new_node = Node(new_value)
        new_node.next = current.next
        current.next = new_node
        self._size += 1
    
    def remove_node(self, value: ValueType) -> ValueType:
        """Оптимизированное удаление узла"""
        if self.head is None:
            raise ValueError("Value not found in list")
        
        # Если удаляем голову
        if self.head.value == value:
            return self.remove_first_node()
        
        # Если удаляем хвост
        if self.tail and self.tail.value == value:
            return self.remove_last_node()
        
        # Обычный поиск и удаление
        current = self.head
        while current.next and current.next.value != value:
            current = current.next
        
        if current.next is None:
            raise ValueError("Value not found in list")
        
        value_to_return = current.next.value
        current.next = current.next.next
        
        # Если удалили хвост, обновляем tail
        if current.next is None:
            self.tail = current
        
        self._size -= 1
        return value_to_return


# ===== ТЕСТИРОВАНИЕ =====
def test_versions():
    print("=== Тестирование всех версий ===")
    
    # Тест версии 1
    print("\n--- Версия 1 ---")
    list1 = SingleLinkedListV1()
    list1.insert_first_node(3)
    list1.insert_first_node(2)
    list1.insert_first_node(1)
    list1.insert_last_node(4)
    print(f"Список: {list1}")
    print(f"Удален первый: {list1.remove_first_node()}")
    print(f"Удален последний: {list1.remove_last_node()}")
    print(f"Список после удалений: {list1}")
    
    # Тест версии 2
    print("\n--- Версия 2 ---")
    list2 = SingleLinkedListV2()
    for i in range(5):
        list2.insert_last_node(i)
    print(f"Список: {list2}")
    print(f"Размер: {list2.get_size()}")
    print(f"Найден 3: {list2.find_node(3)}")
    list2.replace_node(2, 20)
    print(f"После замены 2 на 20: {list2}")
    print(f"Удален узел 20: {list2.remove_node(20)}")
    print(f"Список после удаления: {list2}")
    
    # Тест версии 4
    print("\n--- Версия 4 ---")
    list4 = SingleLinkedListV4()
    for i in range(1, 6):
        list4.insert_last_node(i)
    print(f"Список: {list4}")
    print(f"Предыдущий для 3: {list4.find_previos_node(3)}")
    print(f"Следующий для 3: {list4.find_next_node(3)}")
    list4.insert_before_node(3, 25)
    list4.insert_after_node(3, 35)
    print(f"После вставки 25 перед 3 и 35 после 3: {list4}")
    
    # Тест версии 5
    print("\n--- Версия 5 ---")
    list5 = SingleLinkedListV5()
    for i in range(3):
        list5.insert_last_node(i)
    print(f"Список: {list5}")
    print(f"Голова: {list5.head.value if list5.head else None}")
    print(f"Хвост: {list5.tail.value if list5.tail else None}")
    
    # Тест версии 6
    print("\n--- Версия 6 ---")
    list6 = SingleLinkedListV6()
    for i in range(1, 4):
        list6.insert_last_node(i)
    print(f"Список: {list6}")
    list6.insert_before_node(2, 15)
    list6.insert_after_node(2, 25)
    print(f"После вставок: {list6}")

if __name__ == "__main__":
    test_versions()
```

**Версия 1 (Базовая):**
Базовые операции: вставка/удаление в начало/конец

**Версия 2 (Поиск и замена):**
Добавлены методы поиска, замены и удаления по значению

Поддержка размера списка

**Версия 3 (Оптимизированный размер):**
O(1) получение размера за счет его хранения

**Версия 4 (Работа с соседями):**
Методы для работы с предыдущими/следующими узлами

Вставка/замена/удаление соседних узлов

**Версия 5 (Ссылка на хвост):**
Добавлен указатель на последний элемент

Оптимизация операций с концом списка

**Версия 6 (Оптимизированные операции):**
Дополнительные оптимизации для операций вставки/удаления

Каждая последующая версия наследуется от предыдущей и добавляет новую функциональность или оптимизации, соответствующие требованиям в описании задачи.


### **Задание 2-3**

```python
class ListNode:
    """Узел линейного списка"""
    def __init__(self, value=0, next=None):
        self.value = value
        self.next = next

class LinkedList:
    """Линейный связный список"""
    def __init__(self):
        self.head = None
    
    def append(self, value):
        """Добавление элемента в конец списка"""
        new_node = ListNode(value)
        if not self.head:
            self.head = new_node
            return
        
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node
    
    def print_list(self):
        """Вывод списка"""
        elements = []
        current = self.head
        while current:
            elements.append(str(current.value))
            current = current.next
        print(" -> ".join(elements))
    
    def reverse(self):
        """
        Переворачивает список на месте.
        Временная сложность: O(n)
        Пространственная сложность: O(1)
        """
        prev = None
        current = self.head
        
        while current:
            # Сохраняем ссылку на следующий узел
            next_node = current.next
            # Разворачиваем ссылку
            current.next = prev
            # Перемещаем указатели
            prev = current
            current = next_node
        
        # Обновляем голову списка
        self.head = prev
    
    def sort(self):
        """
        Сортирует список на месте методом пузырьковой сортировки.
        Временная сложность: O(n²)
        Пространственная сложность: O(1)
        """
        if not self.head or not self.head.next:
            return  # Пустой список или список с одним элементом уже отсортирован
        
        swapped = True
        while swapped:
            swapped = False
            current = self.head
            
            while current and current.next:
                if current.value > current.next.value:
                    # Меняем значения местами
                    current.value, current.next.value = current.next.value, current.value
                    swapped = True
                current = current.next

    # Альтернативная реализация сортировки слиянием (более эффективная)
    def merge_sort(self):
        """
        Сортирует список на месте методом слияния.
        Временная сложность: O(n log n)
        Пространственная сложность: O(log n) из-за рекурсии
        """
        self.head = self._merge_sort(self.head)
    
    def _merge_sort(self, head):
        """Вспомогательный метод для сортировки слиянием"""
        # Базовый случай: пустой список или список с одним элементом
        if not head or not head.next:
            return head
        
        # Находим середину списка
        middle = self._get_middle(head)
        next_to_middle = middle.next
        
        # Разделяем список на две половины
        middle.next = None
        
        # Рекурсивно сортируем обе половины
        left = self._merge_sort(head)
        right = self._merge_sort(next_to_middle)
        
        # Объединяем отсортированные половины
        return self._merge(left, right)
    
    def _get_middle(self, head):
        """Находит середину списка"""
        if not head:
            return head
        
        slow = head
        fast = head
        
        while fast.next and fast.next.next:
            slow = slow.next
            fast = fast.next.next
        
        return slow
    
    def _merge(self, left, right):
        """Объединяет два отсортированных списка"""
        if not left:
            return right
        if not right:
            return left
        
        result = None
        
        if left.value <= right.value:
            result = left
            result.next = self._merge(left.next, right)
        else:
            result = right
            result.next = self._merge(left, right.next)
        
        return result


# Демонстрация работы методов
if __name__ == "__main__":
    # Создаем и тестируем список
    lst = LinkedList()
    
    # Добавляем элементы
    for value in [5, 2, 8, 1, 9]:
        lst.append(value)
    
    print("Исходный список:")
    lst.print_list()
    
    # Тестируем reverse
    lst.reverse()
    print("\nПосле reverse:")
    lst.print_list()
    
    # Тестируем сортировку пузырьком
    lst.sort()
    print("\nПосле sort (пузырьковая):")
    lst.print_list()
    
    # Тестируем reverse отсортированного списка
    lst.reverse()
    print("\nПосле reverse отсортированного списка:")
    lst.print_list()
    
    # Создаем новый список для тестирования сортировки слиянием
    lst2 = LinkedList()
    for value in [7, 3, 9, 1, 6, 4]:
        lst2.append(value)
    
    print("\nНовый список для сортировки слиянием:")
    lst2.print_list()
    
    lst2.merge_sort()
    print("После merge_sort:")
    lst2.print_list()
```

**Метод reverse:**
Алгоритм: Итеративно переворачивает ссылки между узлами

Сложность: O(n) по времени, O(1) по памяти

Принцип работы: Использует три указателя (prev, current, next) для изменения направления ссылок

**Метод sort (пузырьковая сортировка):**
Алгоритм: Сравнивает и меняет местами соседние элементы

Сложность: O(n²) по времени, O(1) по памяти

Принцип работы: Проходит по списку несколько раз, пока не останется перестановок

**Метод merge_sort (сортировка слиянием):**
Алгоритм: Делит список пополам, рекурсивно сортирует половины, затем объединяет

Сложность: O(n log n) по времени, O(log n) по памяти (из-за рекурсии)

Принцип работы: Использует стратегию "разделяй и властвуй"

Оба метода сортировки работают "на месте" в том смысле, что они не создают новый список, а перестраивают существующий.


### **Задание 3**

```python
def count_max_elements_single_pass(numbers):
    """
    Однопроходный алгоритм для подсчета количества максимальных элементов в списке.
    
    Args:
        numbers: список действительных чисел
    
    Returns:
        int: количество максимальных элементов
    """
    if not numbers:
        return 0
    
    max_value = numbers[0]
    count = 1
    
    for i in range(1, len(numbers)):
        current = numbers[i]
        
        if current > max_value:
            max_value = current
            count = 1
        elif current == max_value:
            count += 1
    
    return count


# ТЕСТИРОВАНИЕ - 14 ТЕСТОВ
def run_14_tests():
    """Запуск 14 тестов для однопроходного алгоритма"""
    
    print("=" * 60)
    print("ТЕСТИРОВАНИЕ ОДНОПРОХОДНОГО АЛГОРИТМА (14 ТЕСТОВ)")
    print("=" * 60)
    
    # 14 тестовых случаев
    test_cases = [
        # Тест 1: Обычный случай
        ([1.5, 2.3, 2.3, 1.1, 2.3], 3, "Несколько максимальных элементов"),
        
        # Тест 2: Один максимальный
        ([5.0, 4.0, 3.0, 2.0, 1.0], 1, "Один максимальный элемент"),
        
        # Тест 3: Все элементы равны
        ([1.0, 1.0, 1.0, 1.0, 1.0], 5, "Все элементы равны"),
        
        # Тест 4: Один элемент
        ([3.14], 1, "Один элемент в списке"),
        
        # Тест 5: Пустой список
        ([], 0, "Пустой список"),
        
        # Тест 6: Отрицательные числа
        ([-1.5, -2.3, -1.5, -3.0], 2, "Отрицательные числа с повторениями"),
        
        # Тест 7: Отрицательные с одним максимумом
        ([-5.0, -1.0, -10.0, -1.0], 2, "Максимум среди отрицательных"),
        
        # Тест 8: Нули
        ([0.0, -0.0, 0.0], 3, "Ноль и отрицательный ноль"),
        
        # Тест 9: Маленькие числа
        ([1e-10, 1e-10, 2e-10], 1, "Очень маленькие числа"),
        
        # Тест 10: Большие числа
        ([1000000.0, 1000000.0, 500000.0], 2, "Большие числа"),
        
        # Тест 11: Максимум в начале
        ([10.0, 5.0, 3.0], 1, "Максимум в начале"),
        
        # Тест 12: Максимум в конце
        ([5.0, 3.0, 10.0], 1, "Максимум в конце"),
        
        # Тест 13: Максимум в середине
        ([5.0, 10.0, 3.0], 1, "Максимум в середине"),
        
        # Тест 14: Максимум в начале и конце
        ([10.0, 5.0, 10.0], 2, "Максимум в начале и конце")
    ]
    
    passed = 0
    failed = 0
    
    for i, (numbers, expected, description) in enumerate(test_cases, 1):
        result = count_max_elements_single_pass(numbers)
        
        if result == expected:
            print(f"ТЕСТ {i:2d}: ✓ ПРОЙДЕН - {description}")
            print(f"       Вход: {numbers}")
            print(f"       Результат: {result}")
            passed += 1
        else:
            print(f"ТЕСТ {i:2d}: ✗ НЕ ПРОЙДЕН - {description}")
            print(f"       Вход: {numbers}")
            print(f"       Ожидалось: {expected}, Получено: {result}")
            failed += 1
        
        if i < len(test_cases):  # Не печатать разделитель после последнего теста
            print("-" * 50)
    
    print("\n" + "=" * 60)
    print("ИТОГИ ТЕСТИРОВАНИЯ:")
    print(f"Всего тестов: {len(test_cases)}")
    print(f"Пройдено: {passed}")
    print(f"Не пройдено: {failed}")
    print(f"Успешность: {passed/len(test_cases)*100:.1f}%")
    print("=" * 60)

if __name__ == "__main__":
   
    run_14_tests()
    
   
```

### ** Задание 5**

```python
class Node:
    """Узел кольцевого двусвязного списка"""
    def __init__(self, data=None):
        self.data = data
        self.next = None
        self.prev = None

    def __str__(self):
        return f"Node({self.data})"


class CircularDoublyLinkedList:
    """Кольцевой двусвязный список с выделенным головным элементом"""
    
    def __init__(self):
        # Создаем головной элемент (не содержит данных)
        self.head = Node()
        # В пустом списке head ссылается сам на себя
        self.head.next = self.head
        self.head.prev = self.head
        self.size = 0
    
    def is_empty(self):
        """Проверка на пустоту"""
        return self.size == 0
    
    def __len__(self):
        """Возвращает количество элементов (исключая головной)"""
        return self.size
    
    def insert_first(self, data):
        """Вставка элемента в начало списка (после головного)"""
        new_node = Node(data)
        
        # Связываем новый узел
        new_node.next = self.head.next
        new_node.prev = self.head
        
        # Обновляем связи соседних узлов
        self.head.next.prev = new_node
        self.head.next = new_node
        
        self.size += 1
        return new_node
    
    def insert_last(self, data):
        """Вставка элемента в конец списка (перед головным)"""
        new_node = Node(data)
        
        # Связываем новый узел
        new_node.next = self.head
        new_node.prev = self.head.prev
        
        # Обновляем связи соседних узлов
        self.head.prev.next = new_node
        self.head.prev = new_node
        
        self.size += 1
        return new_node
    
    def insert_after(self, node, data):
        """Вставка элемента после указанного узла"""
        if node is None:
            return self.insert_first(data)
            
        new_node = Node(data)
        
        # Связываем новый узел
        new_node.next = node.next
        new_node.prev = node
        
        # Обновляем связи соседних узлов
        node.next.prev = new_node
        node.next = new_node
        
        self.size += 1
        return new_node
    
    def insert_before(self, node, data):
        """Вставка элемента перед указанным узлом"""
        if node is None or node == self.head:
            return self.insert_last(data)
            
        new_node = Node(data)
        
        # Связываем новый узел
        new_node.next = node
        new_node.prev = node.prev
        
        # Обновляем связи соседних узлов
        node.prev.next = new_node
        node.prev = new_node
        
        self.size += 1
        return new_node
    
    def remove(self, node):
        """Удаление указанного узла"""
        if node is None or node == self.head or self.is_empty():
            return None
        
        # Сохраняем данные для возврата
        data = node.data
        
        # Перелинковываем соседние узлы
        node.prev.next = node.next
        node.next.prev = node.prev
        
        # Очищаем связи удаляемого узла
        node.next = None
        node.prev = None
        
        self.size -= 1
        return data
    
    def remove_first(self):
        """Удаление первого элемента"""
        return self.remove(self.head.next) if not self.is_empty() else None
    
    def remove_last(self):
        """Удаление последнего элемента"""
        return self.remove(self.head.prev) if not self.is_empty() else None
    
    def find(self, data):
        """Поиск первого узла с указанными данными"""
        current = self.head.next
        while current != self.head:
            if current.data == data:
                return current
            current = current.next
        return None
    
    def traverse_forward(self):
        """Обход списка в прямом направлении"""
        result = []
        current = self.head.next
        while current != self.head:
            result.append(current.data)
            current = current.next
        return result
    
    def traverse_backward(self):
        """Обход списка в обратном направлении"""
        result = []
        current = self.head.prev
        while current != self.head:
            result.append(current.data)
            current = current.prev
        return result
    
    def get_first(self):
        """Получить первый элемент"""
        return self.head.next if not self.is_empty() else None
    
    def get_last(self):
        """Получить последний элемент"""
        return self.head.prev if not self.is_empty() else None
    
    def rotate_forward(self, steps=1):
        """Повернуть список вперед на указанное количество шагов"""
        if self.is_empty() or steps == 0:
            return
            
        steps = steps % self.size
        for _ in range(steps):
            # Перемещаем первый элемент в конец
            first = self.head.next
            last = self.head.prev
            
            # Перелинковываем
            self.head.next = first.next
            first.next.prev = self.head
            
            first.next = self.head
            first.prev = last
            
            last.next = first
            self.head.prev = first
    
    def __str__(self):
        """Строковое представление списка"""
        if self.is_empty():
            return "CircularDoublyLinkedList: []"
        
        elements = []
        current = self.head.next
        while current != self.head:
            elements.append(str(current.data))
            current = current.next
        
        return f"CircularDoublyLinkedList: [{', '.join(elements)}] (size: {self.size})"


# Демонстрация работы
if __name__ == "__main__":
    # Создаем кольцевой список
    cdll = CircularDoublyLinkedList()
    
    print("Создание и заполнение списка:")
    cdll.insert_first(1)
    cdll.insert_last(2)
    cdll.insert_last(3)
    cdll.insert_first(0)
    print(cdll)
    print("Прямой обход:", cdll.traverse_forward())
    print("Обратный обход:", cdll.traverse_backward())
    
    print("\nВставка элементов:")
    second_node = cdll.find(2)
    cdll.insert_after(second_node, 2.5)
    cdll.insert_before(second_node, 1.5)
    print(cdll)
    
    print("\nУдаление элементов:")
    cdll.remove_first()
    cdll.remove_last()
    node_to_remove = cdll.find(2)
    cdll.remove(node_to_remove)
    print(cdll)
    
    print("\nПоворот списка:")
    cdll.insert_last(4)
    cdll.insert_last(5)
    print("До поворота:", cdll.traverse_forward())
    cdll.rotate_forward(2)
    print("После поворота на 2:", cdll.traverse_forward())
    
    print("\nПроверка кольцевой структуры:")
    first = cdll.get_first()
    last = cdll.get_last()
    print(f"Первый элемент: {first.data}")
    print(f"Последний элемент: {last.data}")
    print(f"Следующий за последним: {last.next.data} (головной элемент)")
    print(f"Предыдущий перед первым: {first.prev.data} (головной элемент)")
```

**Ключевые особенности реализации:**
Выделенный головной элемент: Облегчает операции вставки/удаления и обеспечивает единообразие обработки граничных случаев.

Двусвязность: Каждый узел имеет ссылки на следующий и предыдущий элементы.

Кольцевая структура:

Последний элемент ссылается на первый через головной элемент

Первый элемент ссылается на последний через головной элемент

Нет NULL-ссылок

**Основные операции:**

Вставка в начало/конец/после/перед

Удаление первого/последнего/произвольного

Поиск элемента

Обход в прямом и обратном направлениях

Поворот списка

**Преимущества:**

Быстрая вставка/удаление с обоих концов (O(1))

Удобный обход в обоих направлениях

Единообразная обработка всех случаев

Эта реализация эффективно решает типичные задачи для кольцевых структур данных, такие как реализация буферов, планировщиков задач и циклических алгоритмов.


### **Контрольные вопросы**

<!-- #region -->
**1. Определение линейного списка. Отличие от массива**
Линейный список — это структура данных, состоящая из набора элементов данных (узлов), в которой каждый элемент хранит помимо своих данных ссылку (указатель) на следующий (а возможно, и на предыдущий) элемент последовательности.

Принципиальное отличие от массива:

Организация в памяти: Элементы массива хранятся в непрерывной области памяти, что позволяет использовать арифметику указателей для мгновенного доступа. Элементы списка могут быть разбросаны по памяти произвольным образом, а связь между ними организована через указатели.

Доступ к элементам: В массиве доступ по индексу происходит за O(1). В списке для доступа к i-му элементу нужно пройти от начала через i узлов, что занимает O(n).

Динамичность: Размер массива часто фиксирован (в статических массивах) или требует перевыделения всей памяти при изменении (в динамических). Список является по-настояственно динамической структурой: новые элементы можно добавлять и удалять в любом месте, выделяя и освобождая память только под конкретный узел.

**2. Что такое «узел» (node)?**
Узел — это элементарный блок, из которого строится связный список. Он инкапсулирует данные и связь с соседними узлами.

Поля узла:

В односвязном списке:

data (данные) — полезная информация, которую хранит узел.

next (следующий) — указатель на следующий узел в списке. Для последнего узла этот указатель равен NULL (или nullptr в C++).

В двусвязном списке:

data (данные) — полезная информация.

next (следующий) — указатель на следующий узел.

prev (предыдущий) — указатель на предыдущий узел. Для первого узла этот указатель равен NULL.

**3. Что такое «голова» (head) списка?**
Голова (head) — это указатель на первый узел связного списка. Это отправная точка для любого обхода или операции со списком. Если список пуст, head равен NULL. Роль головы критически важна, так как потеря этого указателя означает потерю доступа ко всему списку.

**4. Для чего хранится указатель на «хвост» (tail)?**
Указатель «хвост» (tail) хранит адрес последнего узла списка. Он используется для ускорения операций добавления элемента в конец списка. Без tail для вставки в конец необходимо обойти весь список от головы до конца (O(n)), а с tail эта операция выполняется за O(1).

**5. Основные виды линейных списков (по типу связности)**
Односвязный список (Singly Linked List): Каждый узел содержит указатель только на следующий узел.

Двусвязный список (Doubly Linked List): Каждый узел содержит указатели как на следующий, так и на предыдущий узел.

Кольцевой/циклический список (Circular Linked List):

Односвязный кольцевой: Указатель next последнего узла указывает на первый узел.

Двусвязный кольцевой: Указатель next последнего узла указывает на первый, а указатель prev первого узла указывает на последний.

**6. Алгоритм добавления в начало односвязного списка (O(1))**
Создать новый узел new_node.

Записать в его поле data нужное значение.

Установить поле next нового узла на текущий первый узел (на который указывает head): new_node->next = head.

Обновить head, чтобы он указывал на новый узел: head = new_node.

Почему O(1)? Эта операция выполняется за константное время, независимо от длины списка, так как требует фиксированного числа действий и всегда известен адрес головы.

**7. Алгоритм добавления в конец односвязного списка без tail (O(n))**
Создать новый узел new_node.

Установить его next в NULL (так как он будет последним).

Если список пуст (head == NULL), то просто сделать head = new_node.

Иначе, обойти список, начиная с head, до последнего узла (у которого next == NULL). Пусть last — указатель на этот узел.

Установить last->next = new_node.

Почему O(n)? В худшем случае (когда в списке много элементов) необходимо пройти от начала до конца всего списка, что требует n шагов.

**8. Удаление узла из односвязного списка**
Причина необходимости указателя на предыдущий элемент: В односвязном списке узел "не знает" о своем предыдущем узле. Если мы просто удалим узел B (на который у нас есть указатель), то узел A, который был до него, будет по-прежнему указывать на несуществующую (удаленную) память. Это приведет к ошибке. Нам нужно изменить поле next узла A, чтобы он указывал на узел C, следующий за B.

Алгоритм (когда известен предыдущий узел prev):

Проверить, что узлы не NULL.

Связать узел prev с узлом, следующим за удаляемым: prev->next = node_to_delete->next.

Освободить память, занимаемую node_to_delete.

**9. Преимущество двусвязного списка и алгоритм удаления**
Ключевое преимущество: В двусвязном списке узел содержит указатель на предыдущий элемент, поэтому для удаления произвольного узла не нужно искать его предыдущий элемент — он уже известен.

Алгоритм удаления (дан указатель node на удаляемый узел):

Если node->next не NULL, то установить node->next->prev = node->prev.

Если node->prev не NULL, то установить node->prev->next = node->next.

Важно: Если node является головой (head), нужно обновить head на node->next.

Важно: Если node является хвостом (tail), нужно обновить tail на node->prev.

Освободить память, занимаемую node.

**10. Что такое «кольцевой/циклический список»?**
Кольцевой список — это список, в котором последний узел указывает на первый (в односвязном) или первый и последний узлы ссылаются друг на друга (в двусвязном). В таком списке нет NULL-указателей в полях связей next/prev.

Особенности обработки:

Обход: Необходимо иметь точку останова (например, начать с головы и остановиться, когда снова вернетесь к голове), иначе попадете в бесконечный цикл.

Проверка на пустоту: Список пуст, если head == NULL. В непустом списке head->next никогда не NULL.

**11. Таблица асимптотической сложности операций**
Операция	          Односвязный (без tail)	Односвязный (с tail)	Двусвязный (с tail)
Вставка в начало	           O(1)	                  O(1)	                 O(1)
Вставка в конец                O(n)	                  O(1)	                 O(1)
Удаление из начала	           O(1)	                  O(1)	                 O(1)
Удаление из конца	           O(n)                   O(n)*	                 O(1)
Поиск элемента по значению	   O(n)	                  О(n)	                 O(n)
Вставка после известного узла  O(1)	                  O(1)	                 O(1)
Удаление известного узла	   O(n)**	              O(n)**	             O(1)
Доступ к элементу по индексу   O(n)	                  O(n)	                 O(n)


*Удаление из конца в односвязном списке с tail все равно требует O(n), так как для обновления tail нужен указатель на предыдущий элемент (который становится новым хвостом).
**Удаление известного узла в односвязном списке требует O(n) для поиска предыдущего узла, если он не предоставлен.

**13. Объяснение сложности доступа по индексу**
Массив O(1): Адрес i-го элемента вычисляется по формуле адрес_начала + i * размер_элемента. Это одна арифметическая операция.

Список O(n): Чтобы добраться до i-го элемента, нужно стартовать с головы и i раз последовательно перейти по ссылке next.

**13. Объяснение сложности вставки**
В начало O(1): Адрес головы всегда известен. Операция требует создания одного нового узла и перенаправления двух указателей.

В конец (без tail) O(n): Адрес последнего элемента неизвестен. Чтобы его найти, нужно пройти через все n узлов списка.

**14. Преимущества и недостатки списка vs массива**
Критерий	
Связный список	                                       Массив
Преимущества	
• Динамический размер                         • Быстрый доступ по индексу (O(1))
• Вставка/удаление в начале за O(1)           • Локальность данных (кэш-эффективность)
• Не требует непрерывной памяти               • Меньший overhead на элемент (нет указателей)
Недоститки 
• Медленный доступ по индексу (O(n))          • Фиксированный размер (или дорогое перевыделение)
• Нет локальности данных (кэш-промахи)        • Вставка/удаление в начале/середине требует сдвига (O(n))
• Дополнительная память на указатели
**15. Примеры эффективного использования списков**
Стек (LIFO): Реализуется на основе односвязного списка с операциями push (в начало) и pop (из начала), которые выполняются за O(1).

Очередь (FIFO): Реализуется на основе односвязного списка с tail (вставка в конец O(1), удаление из начала O(1)) или двусвязного списка.

Плейлист или история браузера: Двусвязный список идеален для навигации "вперед-назад".

Системный аллокатор (управление памятью): Списки свободных блоков памяти.

**16. Причина использования двусвязных списков в сложных структурах**
Двусвязные списки позволяют эффективно (за O(1)) удалять произвольный узел и вставлять элементы как до, так и после известного узла. Это критически важно для:

Deque (двусторонняя очередь): Нужны быстрые вставки и удаления с обоих концов (O(1)).

Метод цепочек в хеш-таблицах: При частых удалениях двусвязный список делает эту операцию намного эффективнее.

**17. «Фиктивная» нода (Dummy Node)**
Фиктивная нода — это узел, не содержащий полезных данных, который помещается в начало (иногда и в конец) списка. Ее поля data не используются, а next указывает на реальную голову списка.

Преимущество: Упрощает код, устраняя множество проверок на NULL. Например, операция вставки в начало или удаления первого элемента становится частным случаем вставки/удаления в середину, так как у первого реального элемента всегда есть "предыдущий" узел (фиктивный).

**18. Алгоритм реверсирования односвязного списка**
Алгоритм (итеративный, без дополнительной памяти O(1)):

Инициализировать три указателя: prev = NULL, current = head, next = NULL.

Пока current != NULL:
a. Сохранить следующий узел: next = current->next.
b. Развернуть указатель текущего узла: current->next = prev.
c. Сдвинуть указатели на один шаг вперед: prev = current, current = next.

Обновить голову: head = prev (теперь prev указывает на новую голову — старый хвост).

**19. Алгоритм определения цикла (Floyd's Cycle-Finding)**
Алгоритм "Черепаха и Заяц":

Создать два указателя, оба указывают на голову списка: slow и fast.

В цикле, пока fast != NULL и fast->next != NULL:
a. slow делает один шаг: slow = slow->next.
b. fast делает два шага: fast = fast->next->next.
c. Если slow == fast, значит, в списке есть цикл.

Если fast или fast->next стали NULL, циклов нет.

**20. Поиск среднего элемента за один проход**
Используется модификация алгоритма с двумя указателями:

Указатели slow и fast стартуют с головы.

На каждом шаге slow двигается на 1 узел, fast — на 2.

Когда fast достигнет конца списка (fast == NULL или fast->next == NULL), slow будет указывать на средний элемент.

**21. Проблема удаления узла в односвязном списке без доступа к предыдущему**
Проблема: Невозможно напрямую удалить узел, так как нужно обновить указатель next его предыдущего узла, а он неизвестен.

Решение (Хитрость, O(1)):

Скопировать данные (и указатель next) из следующего узла в текущий удаляемый узел.
node->data = node->next->data;
node->next = node->next->next;

Удалить следующий узел (который теперь является копией исходного).

Ограничение: Не работает, если удаляемый узел является последним в списке. В этом случае без указателя на предыдущий узел обойтись невозможн
<!-- #endregion -->
