[🇬🇧 English](README_en.md) | [🇩🇰 Denmark](README_da.md) | [🇵🇱 Poland](README_pl.md) | [🇪🇪 Estonia](README_et.md)

<div>
  <button onclick="switchLanguage('en')">🇬🇧 English</button>
  <button onclick="switchLanguage('da')">🇩🇰 Denmark</button>
  <button onclick="switchLanguage('pl')">🇵🇱 Poland</button>
  <button onclick="switchLanguage('et')">🇪🇪 Estonia</button>
</div>

<script>
  function switchLanguage(lang) {
    if (lang === 'en') {
      window.location.href = 'README_en.md';
    } else if (lang === 'da') {
      window.location.href = 'README_da.md';
    } else if (lang === 'pl') {
      window.location.href = 'README_pl.md';
    } else if (lang === 'et') {
      window.location.href = 'README_et.md';
    }
  }
</script>

<p align="center">
  <a aria-label="try my app Typeread" href="https://snack.expo.dev">try my app Typeread</a>
&ensp;•&ensp;
  <a aria-label="try my app public_transport_wro" href="https://docs.expo.dev">try my app public_transport_wro</a>
&ensp;•&ensp;
  <a aria-label="try my app RefV-1" href="https://expo.dev/blog">try my app RefV-1</a>
&ensp;•&ensp;
  <a aria-label="try my app WroGuide" href="https://expo.canny.io/feature-requests">try my app WroGuide</a>
</p>

# Многопоточность в iOS

Многопоточность — это аспект конкурентного программирования, позволяющий программе выполняться в нескольких потоках одновременно. Это улучшает производительность, поскольку позволяет более эффективно использовать процессорное время и проводить операции в фоновом режиме.

Суть многопоточности — разделение работы на меньшие задачи, которые можно выполнять параллельно. Однако важно помнить, что многопоточность не всегда является панацеей. Иногда она приводит к сложностям в управлении состоянием и синхронизации.

Swift предлагает различные методы реализации многопоточности, такие как Grand Central Dispatch (GCD), OperationQueue, NSLock. И низкоуровневые технологии, такие как pthread и NSThread.

## Table of contents

- [ pthread (POSIX Threads)](#-pthread-posix-threads)
- [ NSThread](#-nsthread)
- [ Grand Central Dispatch (GCD)](#-badges)
- [ Operation и OperationQueue](#-contributing)
- [ Swift Concurrency (Swift 5.5 и выше)](#-faq)
- [ RunLoop](#-the-team)
- [Синхронизаторы POSIX](#license)
- [Atomic Operations](#license)
- [Combine Framework](#license)
- [Timer](#license)

## pthread (POSIX Threads)

<p>
pthread (POSIX Threads) — это библиотека, предоставляющая низкоуровневый интерфейс для работы с потоками, которая используется на платформах, совместимых с POSIX (включая macOS и iOS). Она позволяет создавать, управлять потоками и синхронизировать их, обеспечивая полный контроль над многопоточностью.</p>

##### Oсобенности pthread:

- Потоки (Threads): Позволяет создавать и завершать потоки, а также управлять их атрибутами.
- Синхронизация (Synchronization): Включает различные механизмы синхронизации, такие как мьютексы, условные переменные, барьеры и блокировки чтения-записи.
- Управление атрибутами: pthread позволяет управлять атрибутами потоков, такими как приоритет, размер стека, политика планирования и другие параметры.

---

##### 1.Создание потоков

Функция pthread_create используется для создания нового потока.

> import Foundation
>
> var thread: pthread_t?
>
> func threadFunction(\_ arg: UnsafeMutableRawPointer) -> UnsafeMutableRawPointer? {

     print("Поток выполнен")
     return nil

> }
>
> // Создание потока
> pthread_create(&thread, nil, { pointer in

    threadFunction(pointer)

> }, nil)

- pthread_t — это тип дескриптора потока.
- Первый аргумент функции pthread_create — указатель на дескриптор потока.
- Второй аргумент — указатель на атрибуты потока (pthread_attr_t), может быть nil для использования атрибутов по умолчанию.
- Третий аргумент — функция, которую будет выполнять поток.
- Четвёртый аргумент — аргумент, передаваемый в функцию потока.

---

##### 2.Завершение потоков

Для завершения потока используется функция pthread_exit.

> pthread_exit(nil)

Если нужно дождаться завершения другого потока, используется

> pthread_join.

Если нужно дождаться завершения другого потока, используется pthread_join.

> if let thread = thread {

    pthread_join(thread, nil)

}

---

##### 3.Мьютексы (Mutexes)

Мьютексы используются для синхронизации доступа к общим ресурсам.

- Создание мьютекса:

  > var mutex = pthread_mutex_t()
  > pthread_mutex_init(&mutex, nil)

- Блокировка и разблокировка мьютекса:

  > pthread_mutex_lock(&mutex)
  > // Критическая секция
  > pthread_mutex_unlock(&mutex)

- Уничтожение мьютекса:
  > pthread_mutex_destroy(&mutex)

---

##### 4.Условные переменные (Condition Variables)

Условные переменные используются для организации ожидания событий в потоках.

- Создание и инициализация условной переменной:

  > var cond = pthread_cond_t()
  > pthread_cond_init(&cond, nil)

- Ожидание события:

  > pthread_mutex_lock(&mutex)
  > pthread_cond_wait(&cond, &mutex)
  > pthread_mutex_unlock(&mutex)

- Сигнализация события:

  > pthread_cond_signal(&cond)

- Уничтожение условной переменной:
  > pthread_cond_destroy(&cond)

---

##### 5.Барьеры (Barriers)

Барьеры синхронизации (pthread_barrier_t) используются для ожидания завершения всех потоков перед продолжением выполнения.

- Создание и инициализация барьера:

  > var barrier = pthread_barrier_t()
  > pthread_barrier_init(&barrier, nil, 5) // 5 — количество потоков, которые должны достичь барьера

- Ожидание на барьере:

  > pthread_barrier_wait(&barrier)

- Уничтожение барьера:
  > pthread_barrier_destroy(&barrier)

---

##### 6.Потоковые локальные данные (Thread-Local Storage)

POSIX предоставляет способ хранения данных, уникальных для каждого потока, с помощью функции pthread_key_t.

- Создание ключа для потоковых данных:

  > var key = pthread_key_t()
  > pthread_key_create(&key, nil)

- Установка и получение значения:

  > pthread_setspecific(key, value)
  > let value = pthread_getspecific(key)

- Удаление ключа:
  > pthread_key_delete(key)

---

<p>
Когда использовать pthread в iOS разработке?
pthread предоставляет мощный и гибкий API для управления потоками, но в большинстве случаев он считается слишком низкоуровневым для iOS-разработки. Использование pthread может быть оправдано, если:

- Необходимо полное управление потоками и синхронизацией на низком уровне.
- Вы имеете дело с существующим кодом, использующим pthread.
- Требуется максимальная производительность в критически важных частях приложения.
</p>

---

## NSThread

<p>

NSThread — это класс из Foundation framework, предоставляющий объектно-ориентированный интерфейс для работы с потоками в iOS и macOS. Он обеспечивает более высокоуровневое управление потоками по сравнению с pthread, предлагая API, интегрированный с Objective-C и Swift.

</p>
##### Основные возможности NSThread :
- Создание и управление потоками: NSThread позволяет легко создавать потоки и управлять ими, используя удобные методы и свойства.
- Управление атрибутами потока: Вы можете задать приоритеты потоков, имена и настроить поведение выполнения.
- Обработка циклов событий: NSThread поддерживает выполнение циклов событий (RunLoop), что позволяет потоку обрабатывать события, такие как таймеры или сетевые запросы.
- Блокировка и синхронизация: Позволяет безопасно выполнять код в многопоточных приложениях, предотвращая состояния гонки.

##### Основные методы и свойства NSThread :

- init(target:selector:object:): Инициализирует поток, который выполняет указанный метод (селектор) объекта-цели.
- start(): Запускает поток.
- isExecuting: Возвращает true, если поток выполняется.
- isFinished: Возвращает true, если поток завершён.
- isCancelled: Возвращает true, если поток отменён.
- cancel(): Отменяет выполнение потока.
- sleep(forTimeInterval:): Останавливает выполнение потока на заданное время.
- threadPriority: Свойство, позволяющее задать приоритет потока (от 0.0 до 1.0).
- current: Возвращает текущий поток.
- isMainThread: Возвращает true, если поток является главным.

---

##### Создание потоков с использованием NSThread :

##### 1.Создание и запуск потока

Можно создать поток, используя инициализатор init(target:selector:object:), а затем вызвать метод start() для его запуска:

> class MyClass {

    @objc func doWork() {
        print("Работа в отдельном потоке: \(Thread.current)")
    }

}

> let myObject = MyClass()
> let thread = Thread(target: myObject, selector: #selector(MyClass.doWork), object: nil)
> thread.start() // Запуск потока

---

##### 2.Запуск потока с помощью метода detachNewThreadSelector

Можно также использовать метод класса detachNewThreadSelector(\_:toTarget:with:), чтобы создать и запустить новый поток в один шаг:

> Thread.detachNewThreadSelector(#selector(MyClass.doWork), toTarget: myObject, with: nil)

---

##### 3.Использование блоков и замыканий

Для запуска простых задач в новом потоке можно использовать блоки или замыкания:

> Thread.detachNewThread {

    print("Работа в новом потоке с помощью замыкания: \(Thread.current)")

}

---

##### Управление потоком :

##### 1.Отмена выполнения потока

Метод cancel() позволяет отменить выполнение потока. Однако важно помнить, что этот метод не завершает выполнение потока мгновенно — поток должен проверять, был ли он отменён, используя свойство isCancelled.

> let thread = Thread(target: myObject, selector: #selector(MyClass.doWork), object: nil)
> thread.start()

// Позже в коде
thread.cancel() // Отменить выполнение потока

---

##### 2.Управление приоритетом потока

Приоритет потока можно задать с помощью свойства threadPriority, которое принимает значение от 0.0 (низкий приоритет) до 1.0 (высокий приоритет).

> thread.threadPriority = 0.8 // Установка приоритета потока

---

##### 3.Остановка потока на время

Метод sleep(forTimeInterval:) позволяет остановить выполнение потока на заданное количество секунд:

> Thread.sleep(forTimeInterval: 2.0) // Остановка потока на 2 секунды

---

##### Использование цикла событий (RunLoop) с NSThread

Каждый поток, созданный с помощью NSThread, имеет собственный RunLoop, который можно использовать для обработки событий, таких как таймеры или сетевые запросы.

Пример использования RunLoop:

> class MyThreadClass {

    func startThread() {
        let thread = Thread(target: self, selector: #selector(threadMain), object: nil)
        thread.start()
    }

> @objc func threadMain() {

        let runLoop = RunLoop.current
        Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { timer in
            print("Таймер сработал в потоке: \(Thread.current)")
        }
        runLoop.run() // Запуск цикла событий потока
    }

}

---

##### Синхронизация потоков

Для синхронизации доступа к общим ресурсам можно использовать NSLock, NSRecursiveLock, NSCondition и другие классы синхронизации из Foundation:

> class SafeCounter {

    private var value = 0
    private let lock = NSLock()

> func increment() {

        lock.lock() // Блокировка
        value += 1
        lock.unlock() // Разблокировка
    }

> func getValue() -> Int {

        return value
    }

}

---

##### Преимущества использования NSThread

- Простота: Более простой и высокоуровневый API по сравнению с pthread.
- Объектно-ориентированный подход: Легко интегрируется с кодом на Objective-C и Swift.
- Интеграция с RunLoop: Позволяет использовать циклы событий для выполнения задач, таких как обработка сетевых запросов или таймеров.
- Контроль над потоком: Позволяет управлять приоритетами, отменять выполнение потоков и обрабатывать различные события.

##### Недостатки использования NSThread

- Низкая производительность: В сравнении с Grand Central Dispatch (GCD) и Swift Concurrency (async/await), NSThread не так оптимизирован для многозадачности и параллельного выполнения.
- Ручное управление ресурсами: Требует большего контроля над управлением потоками и синхронизацией, что может привести к ошибкам.
- Ограниченная поддержка: В большинстве случаев рекомендуется использовать более современные механизмы, такие как GCD или Swift Concurrency.

---

##Grand Central Dispatch
Grand Central Dispatch (GCD) — это мощный фреймворк для управления многопоточностью и параллельным выполнением задач в iOS и macOS. Он был разработан Apple для оптимизации многозадачности, повышения производительности и упрощения разработки многопоточных приложений. GCD обеспечивает асинхронное выполнение задач на уровне системы, что позволяет эффективнее использовать многопроцессорные и многоядерные устройства.

##### Основные концепции и компоненты GCD

GCD основан на концепциях очередей (dispatch queues) и задач (work items), которые позволяют управлять выполнением кода асинхронно или синхронно, серийно или параллельно.

##### Основные компоненты GCD:

1.Dispatch Queues (Очереди задач):

- Serial Queues (Серийные очереди): Выполняют задачи последовательно, одна за другой.
- Concurrent Queues (Параллельные очереди): Выполняют задачи одновременно в параллельном режиме.

  2.Dispatch Groups (Группы задач): Объединяют несколько задач и позволяют отслеживать их завершение.

  3.Dispatch Semaphore (Семафоры): Управляют доступом к ограниченному ресурсу.

  4.Dispatch Sources (Источники событий): Позволяют управлять асинхронными событиями, такими как таймеры, файловые дескрипторы или сигналы.

  5.Dispatch Work Items (Рабочие элементы): Объекты, представляющие задачи, которые могут быть выполнены в очереди.

##### Типы очередей в GCD

Очереди задач — это основная концепция GCD. Очереди управляют выполнением задач и определяют, как эти задачи будут выполняться (асинхронно или синхронно, серийно или параллельно).

##### 1. Main Queue (Главная очередь)

-**DispatchQueue.main**: Это серийная очередь, которая выполняет задачи в главном потоке. Используется для выполнения задач, связанных с пользовательским интерфейсом (UI), таких как обновление экранов или обработка пользовательского ввода.

##### 2. Global Queues (Глобальные очереди)

-**DispatchQueue.global(qos:)**: Параллельные очереди, которые делят задачи по приоритетам (QoS — Quality of Service).
-Уровни QoS:
**.userInteractive**: Высший приоритет, используется для задач, требующих немедленного взаимодействия с пользователем (например, анимации).
**.userInitiated**: Высокий приоритет для задач, инициированных пользователем, но не обязательно требующих немедленного завершения.
**.default**: Средний приоритет по умолчанию.
**.utility**: Низкий приоритет для фоновых задач, таких как загрузка данных или обработка.
**.background**: Самый низкий приоритет для задач, которые могут выполняться в фоне, например, индексация.

##### 3. Custom Queues (Пользовательские очереди)

- Serial Queue (Серийная очередь): Создается для выполнения задач последовательно.

  > let serialQueue = DispatchQueue(label: "com.example.serialQueue")

- Concurrent Queue (Параллельная очередь): Создается для выполнения задач параллельно.
  > let concurrentQueue = DispatchQueue(label: "com.example.concurrentQueue", attributes: .concurrent)

---

##### Методы выполнения задач в GCD

GCD предоставляет несколько способов выполнения задач в очередях:

##### 1. Асинхронное выполнение (async)

Асинхронный вызов не блокирует текущий поток и выполняет задачу в фоновом режиме.

> DispatchQueue.global().async {

    print("Асинхронная задача выполняется в фоне")

}

##### 2. Синхронное выполнение (sync)

Синхронный вызов блокирует текущий поток до завершения выполнения задачи.

> DispatchQueue.global().sync {

    print("Синхронная задача выполнена")

}
Важно: Никогда не вызывайте sync на главной очереди из главного потока, так как это приведет к дедлоку.

---

##### Использование Dispatch Groups для управления несколькими задачами

DispatchGroup позволяет объединить несколько задач и отслеживать их завершение. Это полезно, когда нужно дождаться завершения нескольких асинхронных операций.

> let group = DispatchGroup()
> let queue = DispatchQueue.global()

> group.enter()
> queue.async {

    print("Задача 1 выполняется")
    group.leave()

}

> group.enter()
> queue.async {

    print("Задача 2 выполняется")
    group.leave()

}

> group.notify(queue: DispatchQueue.main) {

    print("Все задачи завершены")

}

##### Использование Dispatch Semaphores для синхронизации

DispatchSemaphore используется для управления доступом к ограниченному ресурсу, позволяя управлять количеством потоков, которые могут одновременно использовать этот ресурс.

> let semaphore = DispatchSemaphore(value: 2) // Разрешает доступ двум потокам одновременно
> let queue = DispatchQueue.global()

> for i in 1...5 {

    queue.async {
        semaphore.wait() // Уменьшает счетчик семафора
        print("Задача \(i) выполняется")
        Thread.sleep(forTimeInterval: 2) // Имитация длительной работы
        semaphore.signal() // Увеличивает счетчик семафора
    }

}

##### Использование Dispatch Work Items

DispatchWorkItem представляет собой объект, который инкапсулирует задачу. Это позволяет отменить выполнение задачи, задать блок завершения и другие параметры.

> let workItem = DispatchWorkItem {

    print("Рабочий элемент выполняется")

}

> DispatchQueue.global().async(execute: workItem)
> workItem.cancel() // Отмена выполнения задачи

##### Использование Dispatch Sources

DispatchSource используется для обработки различных типов событий, таких как таймеры, файловые дескрипторы и сигналы. Это позволяет работать с асинхронными событиями.

> let timerSource = DispatchSource.makeTimerSource(queue: DispatchQueue.global())
> timerSource.setEventHandler {

    print("Таймер сработал")

}
timerSource.schedule(deadline: .now(), repeating: .seconds(1))
timerSource.activate()

---

##### Примеры использования GCD

1.  Асинхронное обновление пользовательского интерфейса
    Выполнение задачи в фоновом потоке и обновление UI в главном потоке:

    > DispatchQueue.global(qos: .background).async {

        let data = fetchDataFromNetwork() // Длительная операция

    >

        DispatchQueue.main.async {
            updateUI(with: data) // Обновление UI в главном потоке
        }

    }

2.  Синхронизация с помощью серийной очереди
    Использование серийной очереди для синхронизации доступа к разделяемому ресурсу:
    > let serialQueue = DispatchQueue(label: "com.example.serialQueue")
    > var sharedResource = 0
    >
    > for \_ in 1...5 {
        serialQueue.async {
            sharedResource += 1
            print("Значение ресурса: \(sharedResource)")
        }
    }

##### Преимущества использования GCD

1. Простота использования: Позволяет легко управлять многопоточностью и асинхронными задачами.
2. Оптимизация производительности: GCD автоматически управляет количеством потоков, основываясь на ресурсах устройства.
3. Параллелизм: Эффективно использует многопроцессорные и многоядерные системы.
4. Интеграция с системой: GCD тесно интегрирован с операционной системой, что позволяет оптимизировать выполнение задач.

##### Недостатки использования GCD

1. Отсутствие полного контроля: Меньше контроля над потоками по сравнению с pthread или NSThread.
2. Потенциальные ошибки многопоточности: Использование неправильных методов (например, sync на главной очереди) может привести к дедлокам или другим ошибкам.
3. Сложность отладки: Труднее отлаживать ошибки многопоточности, такие как состояния гонки и дедлоки.

---

## Operation и OperationQueue

Operation и OperationQueue — это высокоуровневые API в Foundation фреймворке для работы с многозадачностью в iOS и macOS. Они предоставляют более мощный и гибкий способ управления задачами по сравнению с GCD, включая возможность приостановки, отмены, установления зависимостей и приоритетов задач.

##### Основные концепции Operation и OperationQueue

1. Operation: Представляет собой абстрактный класс для инкапсуляции единичной задачи. Вы можете создавать свои задачи, наследуя Operation, и переопределяя метод main() или реализуя асинхронную задачу, переопределяя методы start() и isAsynchronous.
2. OperationQueue: Менеджер для выполнения объектов Operation. Очередь может выполнять задачи как последовательно (serial), так и параллельно (concurrent).

##### Основные преимущества использования Operation и OperationQueue

- Приоритеты задач: Вы можете устанавливать приоритеты выполнения задач.
- Зависимости задач: Позволяет задавать зависимости между задачами, чтобы они выполнялись в определённом порядке.
- Отмена, пауза и возобновление задач: Задачи можно приостанавливать, возобновлять и отменять.
- Кросс-платформенность: Operation и OperationQueue доступны как в iOS, так и в macOS.
- Интеграция с KVO (Key-Value Observing): Поддержка отслеживания состояния задачи (например, isCancelled, isFinished).

##### Использование OperationИспользование Operation

##### 1. Создание задачи с помощью BlockOperation1

BlockOperation позволяет легко создать операцию, инкапсулирующую один или несколько блоков (замыканий):

> let operation = BlockOperation {

    print("Задача выполняется в операции")

}

> operation.start() // Запуск операции

BlockOperation также позволяет добавить несколько блоков:

> let operation = BlockOperation {

    print("Первая задача в блоке")

}

> operation.addExecutionBlock {

    print("Вторая задача в блоке")

}

> operation.start()

##### 2. Наследование от Operation для создания пользовательских задач

Вы можете создать собственный класс задачи, наследуя Operation, и переопределяя метод main():

> class MyCustomOperation: Operation {
> override func main() {
> if isCancelled {
> return // Проверяем, не отменена ли операция
> }
>
>        print("Моя задача выполняется")
>
>        // Выполняем задачу...
>
>        if isCancelled {
>            return // Проверяем снова
>        }
>
>        print("Задача завершена")
>
> }
> }
>
> let customOperation = MyCustomOperation()
> customOperation.start()

Если задача должна быть асинхронной, вы можете переопределить метод start() и свойства isAsynchronous, isExecuting, isFinished.

Пример асинхронной операции:

> class AsyncOperation: Operation {

    private var _isExecuting = false
    private var _isFinished = false

> override var isAsynchronous: Bool { true }

> override private(set) var isExecuting: Bool {

        get { _isExecuting }
        set {
            willChangeValue(for: \.isExecuting)
            _isExecuting = newValue
            didChangeValue(for: \.isExecuting)
        }
    }

> override private(set) var isFinished: Bool {

        get { _isFinished }
        set {
            willChangeValue(for: \.isFinished)
            _isFinished = newValue
            didChangeValue(for: \.isFinished)
        }
    }

> override func start() {

        if isCancelled {
            finish()
            return
        }

>     isExecuting = true

>      // Выполняем асинхронную задачу

        performAsyncTask()
    }

> private func performAsyncTask() {

        DispatchQueue.global().async {
            print("Асинхронная задача выполняется")

     >       // Имитация выполнения задачи
            Thread.sleep(forTimeInterval: 2)

>         self.finish()

        }
    }

> private func finish() {

        isExecuting = false
        isFinished = true
    }}

##### Использование OperationQueue

OperationQueue управляет выполнением объектов Operation. Вы можете добавить несколько операций в очередь, и они будут выполнены в порядке их добавления или в соответствии с установленными зависимостями.

> let queue = OperationQueue()
> queue.maxConcurrentOperationCount = 2 // Максимальное количество параллельно выполняемых задач

> let operation1 = BlockOperation {

    print("Операция 1 выполняется")

}

> let operation2 = BlockOperation {

    print("Операция 2 выполняется")

}

> queue.addOperation(operation1)
> queue.addOperation(operation2)

##### Установка зависимостей между задачами

Можно задать зависимость между операциями, чтобы одна операция началась только после завершения другой:

> let operation1 = BlockOperation {

    print("Операция 1 выполняется")

}

> let operation2 = BlockOperation {

    print("Операция 2 выполняется")

}

> operation2.addDependency(operation1) // Операция 2 будет выполнена после операции 1

> let queue = OperationQueue()
> queue.addOperations([operation1, operation2], waitUntilFinished: false)

##### Приостановка и возобновление выполнения задач

Очередь можно приостановить и возобновить:

> queue.isSuspended = true // Приостановка выполнения задач
> queue.isSuspended = false // Возобновление выполнения задач

##### Отмена выполнения всех задач

Вы можете отменить все задачи в очереди:

> queue.cancelAllOperations()

---

##### Синхронизация задач

OperationQueue также автоматически управляет синхронизацией задач, обеспечивая потокобезопасное выполнение.

> let downloadOperation = BlockOperation {

    print("Загрузка данных")

}

> let parseOperation = BlockOperation {

    print("Парсинг данных")

}

> parseOperation.addDependency(downloadOperation) // Парсинг начнётся после загрузки

> let queue = OperationQueue()
> queue.addOperations([downloadOperation, parseOperation], waitUntilFinished: false)

##### Приоритеты задач

Каждой задаче можно задать приоритет выполнения:

> operation1.queuePriority = .high
> operation2.queuePriority = .low

##### Преимущества использования Operation и OperationQueue

- Гибкость управления задачами: Возможность устанавливать зависимости, приоритеты, приостанавливать и отменять задачи.
- Высокоуровневая абстракция: Облегчает разработку сложных многозадачных приложений по сравнению с низкоуровневыми API, такими как GCD.
- Поддержка асинхронных операций: Упрощает работу с асинхронными задачами.
- KVO-события: Поддерживает наблюдение за состоянием задач, что удобно для реализации сложных сценариев.
- Преимущества производительности: Использует GCD под капотом, предоставляя высокую производительность и эффективность.

##### Недостатки использования Operation и OperationQueue

- Небольшое снижение производительности: Из-за высокой абстракции иногда может потребоваться больше ресурсов, чем при использовании GCD напрямую.
- Усложнённость для простых задач: Если задачи простые, использование GCD может быть более прямым и легким.

---

##Swift Concurrency
Swift Concurrency — это современная модель асинхронного программирования, представленная Apple в Swift 5.5 (и выше). Эта модель предоставляет мощные и удобные инструменты, такие как async/await, Task, Actors и Structured Concurrency для работы с многозадачностью и параллелизмом, обеспечивая более безопасное, читаемое и поддерживаемое написание асинхронного кода.

##### Основные концепции Swift Concurrency

**1. Async/Await**
async/await — это ключевые слова, которые позволяют писать асинхронный код линейно, улучшая его читаемость и управление.

- async: Модификатор, который обозначает, что функция или метод являются асинхронными и могут выполнять потенциально длительные операции.
- await: Используется внутри асинхронной функции для приостановки выполнения до завершения другой асинхронной операции.
  > func fetchData() async -> String {
      // Имитация задержки загрузки данных
      try await Task.sleep(nanoseconds: 1_000_000_000)
      return "Данные загружены"
  }
  > func processData() async {
      let data = await fetchData()
      print(data)
  }

---

**2. Task**
Task представляет собой единицу работы, которая может выполняться асинхронно. В Swift есть несколько типов задач, таких как:

- Task: Главная сущность для создания задач.
- Task и DetachedTask: Для выполнения задач с контекстом приоритета или вне текущего контекста.
- Task.sleep: Метод для создания задержки (пауз) в асинхронном контексте.
  > Task {
      await processData()
      print("Задача завершена")
  }

---

**3. Structured Concurrency**
**Structured Concurrency** означает, что задачи и операции управляются в иерархической структуре, что обеспечивает более безопасное и предсказуемое выполнение. С помощью **async/await** задачи организуются в деревья задач, где родительские задачи могут контролировать свои дочерние.

> func parentTask() async {

    await withTaskGroup(of: String.self) { group in

>        group.addTask {

            await fetchData()

>        }
>
>        group.addTask {

            await fetchData()

>        }

>        for await result in group {

            print(result)
        }
    }}

>

---

**4. Actors**
**Actors** — это новый тип данных, который обеспечивает потокобезопасность для состояния, доступного из разных потоков. Они позволяют ограничивать конкурентный доступ к данным и предотвращают состояния гонки.

> actor Counter {

    private var value = 0

> func increment() {

        value += 1
    }

> func getValue() -> Int {

        return value
    }

}

---

**5. Sendable**
**Sendable** — это протокол, который указывает, что тип может безопасно передаваться между потоками. Все типы данных по умолчанию являются Sendable, если они неизменяемы или явно объявлены таковыми.

##### Основные компоненты Swift Concurrency

1. Async Functions (Асинхронные функции): Функции, которые могут выполняться асинхронно и возвращать значения через await.
2. Async Sequences (Асинхронные последовательности): Асинхронные последовательности, позволяющие выполнять итерации по значениям, которые поступают асинхронно.
3. Actors (Акторы): Обеспечивают потокобезопасный доступ к данным, предотвращая состояния гонки.
4. Tasks (Задачи): Управляют выполнением асинхронного кода, могут быть созданы в синхронных и асинхронных контекстах.
5. Task Groups (Группы задач): Объединяют несколько задач для параллельного выполнения и отслеживания их статусов.

##### Async/Await: Принципы работы

Асинхронные функции (async) позволяют выполнять долгие операции, такие как сетевые запросы, не блокируя основной поток.

> func loadData() async -> String {

    try await Task.sleep(nanoseconds: 1_000_000_000) // Задержка в 1 секунду
    return "Данные загружены"

}

> Task {

    let data = await loadData()
    print(data)

}

##### Использование Task

Создание задачи с помощью Task:

> Task(priority: .high) {

    let data = await loadData()
    print("Загруженные данные: \(data)")

}

##### Отмена задачи:

Задачи могут быть отменены, и разработчики могут проверять состояние отмены, используя Task.isCancelled.

> Task {

    guard !Task.isCancelled else {
        print("Задача отменена")
        return
    }
    print("Выполняем задачу")

}

##### Использование Actors

Actors предоставляют потокобезопасный доступ к данным. Доступ к состоянию актора всегда будет последовательным, что предотвращает состояния гонки.

> actor BankAccount {

    private var balance: Int = 0

> func deposit(amount: Int) {

        balance += amount
    }

> func withdraw(amount: Int) -> Bool {

        if balance >= amount {
            balance -= amount
            return true
        }
        return false
    }

> func getBalance() -> Int {

        return balance
    }

}

Использование актора:

> let account = BankAccount()

> Task {

    await account.deposit(amount: 100)
    let currentBalance = await account.getBalance()
    print("Текущий баланс: \(currentBalance)")

}

##### Параллелизм с помощью Task Group

Task Groups позволяют выполнять несколько задач параллельно и управлять их завершением.

> func fetchAllData() async {

    await withTaskGroup(of: String.self) { group in
        group.addTask {
            await fetchData()
        }

>        group.addTask {

            await fetchData()
        }

>        for await result in group {

            print("Результат: \(result)")
        }
    }}

---

##### Structured Concurrency

Структурированная многозадачность позволяет контролировать выполнение задач и гарантирует, что родительская задача не завершится до тех пор, пока не завершатся все её дочерние задачи.

> func parentFunction() async {

    await withTaskGroup(of: Void.self) { group in
        group.addTask {
            await childTask()
        }
        group.addTask {
            await childTask()
        }
    }
    print("Все дочерние задачи завершены")

}

##### Преимущества использования Swift Concurrency

1. Повышенная читаемость: Линейный код с async/await делает его более читаемым и понятным.
2. Безопасность потоков: Actors и Structured Concurrency предотвращают состояния гонки и другие проблемы многопоточности.
3. Автоматическое управление памятью: Swift Concurrency интегрирован с системой управления памятью Swift, что уменьшает количество ошибок, связанных с памятью.
4. Оптимизация производительности: Использует оптимизированный диспетчер задач для улучшения использования ресурсов и производительности.
5. Гибкость и масштабируемость: Поддержка параллельных задач и управление зависимостями между ними.

##### Недостатки использования Swift Concurrency

1. Требует Swift 5.5 и выше: Swift Concurrency доступен только начиная с версии 5.5.
2. Только для iOS 15 и выше: Новая модель асинхронности требует iOS 15 или выше, что может ограничить поддержку старых устройств.
3. Необходимость переписывания старого кода: Модели на основе GCD и OperationQueue могут потребовать значительных изменений для перехода на новую модель.
