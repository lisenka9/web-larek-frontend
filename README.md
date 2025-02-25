# Проектная работа "Веб-ларек"

Стек: HTML, SCSS, TS, Webpack

Структура проекта:
- src/ — исходные файлы проекта
- src/components/ — папка с JS компонентами
- src/components/base/ — папка с базовым кодом

Важные файлы:
- src/pages/index.html — HTML-файл главной страницы
- src/types/index.ts — файл с типами
- src/index.ts — точка входа приложения
- src/scss/styles.scss — корневой файл стилей
- src/utils/constants.ts — файл с константами
- src/utils/utils.ts — файл с утилитами

## Установка и запуск
Для установки и запуска проекта необходимо выполнить команды

```
npm install
npm run start
```

или

```
yarn
yarn start
```
## Сборка

```
npm run build
```

или

```
yarn build
```

## Описание архитектуры

### 1. Данные

#### Интерфейс `IProduct`

Описывает структуру данных о товаре.

*   **`id`:** `string` - Уникальный идентификатор товара.
*   **`title`:** `string` - Название товара.
*   **`description`:** `string` - Описание товара.
*   **`image`:** `string` - URL изображения товара.
*   **`price`:** `number | null` - Цена товара (может быть `null`, если товар "бесценный").
*   **`category`:** `ProductCategory` - Категория товара.
*   **`index`:** `number` - Порядковый номер продукта.

#### Тип `ProductCategory`

Описывает все варианты категорий продукта. Используется в типе `CategoryClasses`, который описывает запись ключ-значение, где ключ - это название категории, а значение - CSS-класс, содержащий стили поля, отображающего категорию в карточке продукта.

#### Интерфейс `ICardActions`

Описывает событие по клику на карточку.

*   **`onClick`:** `() => void`

#### Интерфейс `IWebLarekApi`

Описывает методы отправки и получения данных с сервера.

*   **`getProductList(): Promise<IProduct[]>`:** Возвращает Promise с массивом данных о товарах.
*   **`getProductInfo(id: string): Promise<IProduct>`:** Возвращает Promise с данными о товаре по его ID.
*   **`orderProducts(orderData: any): Promise<IOrderResult>`:** Отправляет данные о заказе и возвращает результат запроса.

#### Интерфейс `IPage`

Описывает данные и свойства страницы.

*   **`catalog`:** `HTMLElement[]` - Массив HTML-элементов (карточек товара).  
*   **`locked`:** `boolean` - Значение, говорящее о том, заблокирована страница или нет (при открытии модального окна).

#### Интерфейс `IModalView`

Содержит одно поле модального окна - его контент типа HTML-элемента. 

*   **`content`:** `HTMLElement`

#### Интерфейс `IBasketView`

Описывает данные для отображения их в элементах корзины на странице.

*   **`items`:** `HTMLElement[]` - Массив HTML-элементов (карточек продукта, добавленных в корзину).
*   **`total`:** `number` - Общая стоимость всех продуктов в корзине.

#### Интерфейс `IFormState`

Описывает данные, используемые в отображении формы.

*   **`valid`:** `boolean` - Значение, означающее валидны ли введенные данные или нет.
*   **`errors`:** `string` - Строка, описывающая ошибку, если введенные данные некорректны.

#### Интерфейс `IOrderForm`

Описывает данные, используемые в отображении формы заказа.

*   **`methodPayment`:** `string` - Строка, содержащая выбранный метод оплаты.
*   **`address`:** `string` - Строка, содержащая адрес доставки.
*   **`email`:** `string` - Строка, содержащая эл. адрес.
*   **`phone`:** `string` - Строка, содержащая телефон.

#### Интерфейс `IOrder`

Описывает данные заказа.

*   **`items[]`:** Список заказанных продуктов.
*   **`total`:** Общая сумма стоимости заказа.
*   (Также включает все поля из `IOrderForm`).

#### Тип `FormErrors`

Запись ключ-значение, где ключ - это поле формы из интерфейса `IOrderForm`, а значение - текст ошибки.

#### Интерфейс `IOrderResult`

Описывает данные, полученные в результате заказа.

*   **`id`:** `string` - Уникальный идентификатор товара.
*   **`total`:** `number` - Общая сумма стоимости заказа.

#### Интерфейс `ISuccess`

Описывает данные об успешном заказе.

*   **`total`:** `number` - Общая сумма заказа.

### 2. События

*   **`'items:show'`:** Загрузка полученных с сервера данных в каталог на странице.
*   **`'card:select'`:** Сохранение в поле класса состояния данных приложения id выбранной карточки.
*   **`'preview:show'`:** Загрузка превью карточки в модальное окно.
*   **`'product:order'`:** Добавление продукта в корзину.
*   **`'basket:open'`:** Открытие модального окна с корзиной и загрузка выбранных продуктов.
*   **`'card:delete'`:** Удаление продукта из корзины.
*   **`'formErrors:change'`:** Проверка на наличие ошибок в полях формы и вывод текста ошибки.
*   **`'input:change'`:** Отслеживает ввод данных полей формы.
*   **`'order:open'`:** Открытие модального окна с первой формой оформления заказа.
*   **`'order:submit'`:** Открытие модального окна со второй формой оформления заказа.
*   **`'contacts:submit'`:** Отправка введенных данных на сервер и открытие модального окна с подтверждением созданного заказа.
*   **`'modal:open'`:** Блокирует страницу при открытии модального окна.
*   **`'modal:close'`:** Разблокирует страницу при закрытии модального окна.

### 3. Базовые классы

#### Класс `Api`

Служит для связи с сервером, отправления и получения запросов.

*   **Методы:**
    *   `handleResponse(response: Response): Promise<object>`: Первоначальная обработка получаемых данных через метод `json`.
    *   `get(uri: string)`: Получение запроса.
    *   `post(uri: string, data: object, method: ApiPostMethods = 'POST')`: Отправка запроса.

#### Класс `EventEmitter`

Служит для обработки событий.

*   **Методы:**
    *   `on<T extends object>(eventName: EventName, callback: (event: T) => void)`: Установить обработчик на событие.
    *   `off(eventName: EventName, callback: Subscriber)`: Снять обработчик с события.
    *   `emit<T extends object>(eventName: string, data?: T)`: Инициировать событие с данными.
    *   `onAll(callback: (event: EmitterEvent) => void)`: Слушать все события.
    *   `offAll()`:Сбросить все обработчики
    *   `trigger<T extends object>(eventName: string, context?: Partial<T>)`:  Сделать коллбек триггер, генерирующий событие при вызове

#### Абстрактный класс-дженерик `View<T>`

Служит для отрисовки DOM-элементов.

*   **Конструктор:** `constructor(container: HTMLElement)`: Принимает HTML-элемент - контейнер.
*   **Методы:**
    *   `setText(element: HTMLElement, text: string): void`: Устанавливает значение поля `TextContent`.
    *   `setDisabled(element: HTMLElement, disabled: boolean): void`: Включает или выключает блокировку объекта.
    *   `setImage(element: HTMLImageElement, src: string): void`: Устанавливает значения для отображения изображения.
    *   `render(T): HTMLElement`: Принимает данные, которые будут отображаться в элементе, и возвращает этот элемент.

### 4. Компоненты представления

#### Класс `Page` (наследует `View<IPage>`)

Служит для отрисовки и установки значений некоторых элементов на странице.

*   **Поля типа `HTMLElement`:**
    *   `_catalog`: Элемент, в котором содержатся основные элементы-товары.
    *   `_wrapper`: Элемент-оборачивающий страницу.
    *   `_basket`: Элемент-кнопка для открытия корзины.
    *   `_counter`: Элемент на кнопке корзины, отображающий количество товаров в корзине.
*   **Методы:**
    *   `set catalog(elements: HTMLElement[])`: Устанавливает элементы-товары в соответствующий HTML-элемент.
    *   `set locked(value: boolean)`: С помощью добавления класса блокирует страницу.
    *   `set counter(value: number)`: Устанавливает число элементов в корзине как значение в соответствующий HTML-элемент.

#### Класс `Modal` (наследует `View<IModalView>`)

Служит для отрисовки модального окна с контентом.

*   **Поля типа `HTMLElement`:**
    *   `closeButton`: Кнопка закрытия окна.
    *   `content`: Элемент окна с содержимым.
*   **Методы:**
    *   `open()`: Открывает модальное окно.
    *   `close()`: Закрывает модальное окно.
    *   `set content(element: HTMLElement)`: Устанавливает контент окна.
    *   `render()`: Устанавливает данные и возвращает элемент окна.

#### Класс `Card` (наследует `View<IProduct>`)

Служит для отрисовки элемента карточки продукта на странице.

*   **Поля типа `HTMLElement`:**
    *   `_title`:
    *   `_price`:
    *   `_category`:
    *   `_image`:
    *   `_description`:
    *   `_button`:
    *   `_index`:
*   **Методы:**
    *   Сеттеры для полей интерфейса: `id`, `title`, `category`, `image`, `description`, `price`, `index`.
    *   `render()`: К родительскому методу добавлена блокировка кнопки покупки в случае 'бесценного' продукта.

#### Класс `Basket` (наследует `View<IBasketView>`)

Служит для отрисовки элемента корзины.

*   **Поля типа `HTMLElement`:**
    *   `_list`: HTML-элемент, который содержит основные элементы, добавленные в корзину.
    *   `_total`: HTML-элемент, содержащий число, отображающее общую сумму элементов корзины.
    *   `_button`: HTML-элемент - кнопка для оформления корзины.
*   **Методы:**
    *   `set items(elements: HTMLElement[])`: Размещает список элементов корзины в соответствующем HTML-элементе.
    *   `set total(value: number)`: Устанавливает общее число элементов корзины в соответствующем HTML-элементе.

#### Класс-дженерик `Form<T>` (наследует `View<IFormState>`)

Служит базовым классом для отрисовки форм.

*   **Поля типа `HTMLElement`:**
    *   `_submit`:
    *   `_errors`:
*   **Методы:**
    *   `onInputChange()`: Инициализирует событие `'input:change'`.
    *   `render()`:
    *   Сеттеры полей интерфейса: `valid`, `errors`.

#### Класс `Order` (наследует `Form` с частичной реализацией `IOrderForm`)

Служит для отрисовки полей первой формы оформления заказа.

*   **Поле:**
    *   `_methodButtons`: Для хранения массива всех кнопок с вариантами оплаты.
*   **Методы:**
    *   `resetPayment()`: Для сброса со всех кнопок CSS-класса активной кнопки выбора оплаты.
    *   `selectPayment(button: HTMLElement)`: Установка на кнопку CSS-класса активной кнопки выбора оплаты.
    *   `set address(value: string)`:
    *   `render()`: К родительскому методу добавлен метод `resetPayment`.

#### Класс `Contacts` (наследует `Form` с частичной реализацией `IOrderForm`)

Служит для отрисовки полей формы для ввода контактных данных.

*   **Методы:**
    *   Сеттеры полей интерфейса: `email` и `phone`.

#### Класс `Success` (наследует `View`)

Служит для отрисовки модального окна с сообщением о завершении оформления заказа.

*   **Поля:**
    *   `_close`: HTML-элемент - кнопка закрытия окна.
    *   `_total`: Поле для отображения общей суммы заказа.

### 5. Компоненты модели данных

#### Класс `ProductItem`

Основная модель для хранения данных о продукте.

*   **Поля:**
    *   `id`: string;
    *   `title`: string;
    *   `description`: string;
    *   `image`: string;
    *   `price`: number;
    *   `category`: ProductCategory;
    *   `events`: (для хранения возможных для продукта событий).

#### Класс `AppData`

Содержит состояние данных, полученных с сервера, а также в результате событий на странице.

*   **Поля:**
    *   `catalog`: Массив всех объектов класса `ProductItem`.
    *   `preview`: Строка, содержащая id продукта, открытого в отдельной карточке.
    *   `basket`: Массив объектов класса `ProductItem`, добавленных в корзину.
    *   `order`: Объект, содержащий поля с данными обеих форм заказа, а также массив id добавленных в корзину продуктов и поле `total` с общей суммой заказа.
*   **Методы:**
    *   `setCatalog(products: IProduct[])`: Устанавливает в поле `catalog` получаемый как аргумент массив объектов, поддерживающих интерфейс `IProduct`.
    *   `setPreview(productId: string)`: Определяет в поле `preview` id выбранного для просмотра продукта и сообщает об открытии.
    *   `addToBasket(product: ProductItem)`: Добавляет в массив `basket` объект класса `ProductItem`.
    *   `deleteFromBasket(product: ProductItem)`: Удаляет из массива `basket` объект класса `ProductItem`.
    *   `clearBasket()`: Очищает массив объектов класса `ProductItem` в корзине.
    *   `clearOrder()`: Очищает значения всех полей объекта заказа `order`.
    *   `getTotal()`: Подсчитывает общую сумму полей `price` у объектов `ProductItem` в корзине.
    *   `validateOrder()`: Проверяет валидность формы с помощью инициализации события `'formErrors:change'`, возвращает булевое значение.
    *   `setOrderField(field: keyof IOrder, value: any)`: Метод сохранения полученных из формы данных с типом `IOrder` в поля объекта `order`.

#### Класс `WebLarekApi` (наследуется от `Api`, реализует `IGetProductApi`)

Служит для получения информации о продуктах с сервера.

*   **Методы:**
    *   `getProductList(): Promise<IProduct[]>`: Возвращает промис с массивом объектов, реализующих интерфейс `IProduct`.
    *   `getProductInfo(id: string): Promise<IProduct>`: Получает как аргумент id продукта и возвращает промис с объектом, реализующим интерфейс `IProduct`.
    *   `orderProducts(orderData: any): Promise<IOrderResult>`: Отправляет данные заказа на сервер и возвращает промис с объектом, реализующим интерфейс `IOrderResult`.


