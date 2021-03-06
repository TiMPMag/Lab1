# Lab1
**Материалы для лабораторной работы 1**

Задание на ЛР1. Простые методы сортировки и паттерн проектирования «Шаблонный метод» (Template Method)
**Теория**

Шаблонный метод (Template method) – поведенческий шаблон проектирования, основанный на объектно- ориентированном подходе, определяющий основу алгоритма и позволяющий наследникам переопределять некоторые шаги алгоритма, не изменяя его структуру в целом.

Основное назначение:

•	однократное использование неизменяемой части алгоритма, с оставлением изменяющейся части на усмотрение наследникам;

•	локализация и вычленение общего для нескольких классов кода для избегания дублирования;

•	разрешение расширения кода наследниками только в определенных местах.

Многие программы включают следующую фундаментальную структуру главного цикла:

		init(); // Инициализация
		while (isDone())
			idle(); // Что-то делаем на каждой итерации цикла
	        cleanup(); // Освобождаем ресурсы
	
Тогда, эту общую часть можно включить в отдельный метод некоторого абстрактного класса. Например, так
```ShellSession
class A
{
protected:
	virtual void run()
	{
		init(); // Инициализация
		while (isDone())
			idle(); // Что-то делаем на каждой итерации цикла
		cleanup(); // Освобождаем ресурсы
	}
	// Чистые виртуальные функции
	virtual void init() = 0;
	virtual bool isDone() = 0;
	virtual void idle() = 0;
	virtual void cleanup() = 0;
};
```
На базе этого класса можно строить производные классы, в которых чистые виртуальные функции переопределяются, а функция run наследуется и остается неизменной.

Рассмотрим возможный вариант реализации этого паттерна для сортировки массивов. В базовый класс включим алгоритм сортировки методом «пузырька», а в производных классах будем задавать тип элемента массива, который не задан в базовом классе. Заготовки классов могут выглядеть так.

```ShellSession
// Абстрактный класс для сортировки пузырьком (тип элемента массива не известен)
class BubbleSorter
{
public:
	// Печать массива
	virtual void print(std::ostream & out) = 0;
protected:
	int len = 0; // Число элементов массива
	// Меняем местами в массиве элементы с индексами index и index+1
	virtual void swap(int index) = 0;
	// Возвращает true, если элементы с индексами index и index+1, надо поменять местами
	virtual bool outOfOrder(int index) = 0;
	// Собственно сортировка массива
public:
virtual void doSort()
	{
		for (int i = 0; i < len - 1; i++)
		{
			bool flag=false; // Флаг показывает, что был выполнен обмен элементов 
			for (int j = 0; j < len - 1 - i; j++)
				if (outOfOrder(j)) // Меняем элементы местами
				{
					swap(j);
					flag = true;
				}
			if (!flag) // Замен не было, массив уже отсортирован
				break;
		}

	}
	virtual ~BubbleSorter()
	{
	}
};

// Класс для работы с массивов int
class IntBubbleSorter : public BubbleSorter
{
	int * array = nullptr;
protected:
	void swap(int index)
	{
		…..
	}
	bool outOfOrder(int index)
	{
		….
	}
public:
	virtual void print(std::ostream & out)
	{
		…..
	}
	// Далее конструкторы, деструктор и др.
……………………………………………………………………………..

};
```
Для метода прямого выбора абстрактный класс может выглядеть следующим образом:
```ShellSession
// Абстрактный класс для сортировки массива методом прямого выбора (тип элемента массива не известен)
class ArraySorter
{
protected:
	int len = 0; // Число элементов массива
public:
	// Печать массива
	virtual void print(std::ostream & out) = 0;
	// Меняем местами в массиве элементы с индексами index1 и index2
	virtual void swap(int index1, int index2) = 0;
	// Возвращает true, если элементы с индексами index1 и index2 надо поменять местами
	virtual bool outOfOrder(int index1, int index2) = 0;
	
	virtual ~ArraySorter()
	{
	virtual void doSort()
	{
		for (int i = 0; i < len-1; i++)
		{
			int indexMin = i; // Индекс минимального элемента
			// Поиск минимума, начиная с элемента i
			for (int j = i + 1; j < len; j++)
			{
				if (outOfOrder(indexMin, j))  indexMin = j;
			}
			if (indexMin != i) swap(indexMin, i);
		}
	}
};
```
Для метода прямого включения (метод вставки) абстрактный класс может выглядеть следующим образом:
```ShellSession
// Абстрактный класс для сортировки массива методом вставки (тип элемента массива не известен)
class ArraySorter
{
protected:
	int len = 0; // Число элементов массива
public:
	// Печать массива
	virtual void print(std::ostream & out) = 0;
	// Вставка элемента с индексом index2 на место элемента index1, 
// элементы, начиная с index1 сдвигаются на 1 элемент (index1 < index2)
	virtual void insert(int index1, int index2) = 0;
	// Возвращает true, если элементы с индексами index1 и index2 надо поменять местами
	virtual bool outOfOrder(int index1, int index2) = 0;
	
	virtual ~ArraySorter()
	{
	virtual void doSort()
	{
		for (int i = 2; i < len; i++)
		{
			// Ищем, куда вставить элемент среди предыдущих элементов
			for (int j = 0; j < i; j++)
			{
				if (outOfOrder(j, i)) {
					insert(j, i);
					break;
				}
			}
		}
	}
};
```
***Примечание. В приведенных примерах предполагается, что тип элемента массива определяется в производных классах. В языках программирования, поддерживающих шаблоны классов, эту проблему можно проще решить с помощью шаблонов классов.***

**Задание**

Реализовать простейшие методы сортировки массивов, использовать любой язык программирования.  Для сортировки не использовать библиотечные функции, исходный код сортировки должен присутствовать в явном виде в исходном коде всей программы. Массивы размерности n создается динамически (n – задается в исходных данных), массив заполняется псевдослучайными числами. Продемонстрировать работу с массивом целых чисел и с массивом вещественных чисел.

 Для получения высшего балла необходимо применять объектно-ориентированный подход и описанный паттерн проектирования (или шаблоны классов).
 
По результатам работы представляется отчет, отчет должен содержать:

- условие задания;

- исходный код программы с комментариями в алгоритме сортировки;

- результаты работы программы: вывод (на консоль или в файл) массива до сортировки и после сортировки;

- выводы (при необходимости).

Вариант выбирается по формуле: № mod 3, если результат равен 0, то номер варианта 3.

где № – это номер студента в ЭУ (при сортировке группы по алфавиту), mod – операция получения остатка от деления.

Вариант 1. Метод прямого выбора. 

Вариант 2. Метод прямого обмена (метод "пузырька").

Вариант 3. Метод прямого включения.
