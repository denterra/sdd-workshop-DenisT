# Шаблон специфікації аналітика (refinement)

Копіюйте цей файл на кожну фічу/епік. Тримайте ID стабільними в Jira/ADO/wiki-посиланнях.

---

## Заголовок

| Поле | Значення |
|------|----------|
| ID фічі | FEAT-____ |
| Назва | |
| Автор | |
| Статус | Відкриття / Уточнення / Готово |
| Стейкхолдери | |
| Останнє оновлення | |

---

## 1. Постановка проблеми

Відвідувач може надіслати звернення без телефону, а прикріпити резюме чи портфоліо у формі зараз неможливо. Фундація просить зробити телефон обов’язковим, дозволити додати документ і відобразити нове поле у сповіщенні. Очікуваний результат — відвідувач надсилає звернення з документом або без нього, а працівник отримує відповідне сповіщення. Бізнес-мету та метрику з навчальної розмови винесено в ASM-01. [Запит і контекст Дня 2](contact-form-feature.md#1-короткий-опис-і-вхідний-запит)

## 2. Межі

| ID | У межах | Підстава |
| --- | --- | --- |
| IN-01 | Обов’язковий телефон на сторінці «Контакти». | Обов’язковість — RULE-04. Чинний формат заповненого номера описано в RULE-05. Q-22 уточнює, чи приймати міжнародні номери з 7–9 цифрами. |
| IN-02 | Можна додати CV або портфоліо чи надіслати звернення без документа. | RULE-10. Один PDF — умова вправи. Точний ліміт очікує відповіді на Q-01. |
| IN-03 | Сповіщення про звернення відображає нове поле. За наявності документа працівник має змогу відкрити його. | RULE-11, RULE-12. Спосіб доставки, вміст і коло одержувачів уточнюють Q-02, Q-03, Q-05. |

| ID | Виключення або межа, що очікує рішення | Підстава |
| --- | --- | --- |
| OUT-01 | Перенесення контактних правил на партнерські звернення й отримання нот потребує окремого рішення. | Ці потоки використовують спільну форму. Два регресійні приклади збережено в архівному записі AC-12; питання про остаточну межу — Q-09. |

## 3. Актори й потік

| Актор | Роль |
| --- | --- |
| Відвідувач | Заповнює форму, вказує телефон і вирішує, чи додавати документ. |
| Працівник Фундації | Опрацьовує звернення й відкриває доданий документ. Коло уповноважених осіб уточнює Q-05. |

**Очікуваний потік**

1. Відвідувач заповнює контактні поля, надає згоду й за бажанням додає PDF.
2. Форма та сервер перевіряють дані. Поки триває перший запит, повторне натискання кнопки не надсилає другого запиту (AC-22).
3. У сценарії Дня 2 після прийняття листа поштою відвідувач бачить підтвердження. Доставку працівнику перевіряють окремо за листом у скриньці.
4. Якщо надано PDF, працівник відкриває його зі сповіщення. Спосіб передавання залежить від Q-03.

**Винятки.** Коректне звернення без PDF має окремий шлях (AC-16, AC-17). Помилки телефону, вкладення і згоди перевіряють окремо. При відмові пошти відвідувач має побачити помилку, а підтвердження успіху має залишитися прихованим (AC-10, AC-25). Долю введених даних і PDF після збою та невідомий результат після обриву зв’язку уточнюють Q-04, Q-19, Q-21.

## 4. Бізнес-правила

Колонки «Є зараз» і «Має бути» перенесено з [таблиці правил Дня 2](contact-form-feature.md#5-таблиця-правил). Лише для RULE-04, RULE-10 і RULE-11 джерело фіксує рішення замовника за умовою вправи. Для інших правил опис реалізації слугує базою порівняння.

| ID | Предмет | Є зараз | Має бути / статус рішення |
| --- | --- | --- | --- |
| RULE-01 | Довжина імені | Обов’язкове поле, від 2 до 50 символів. UI прибирає крайні пробіли, API рахує довжину отриманого рядка. | Рішення не погоджено. |
| RULE-02 | Формат електронної адреси | Адреса обов’язкова. Браузер застосовує перевірку Zod, сервер — власний вираз. Наприклад, сервер відхиляє адресу з плюсом перед @. | Рішення не погоджено. Q-08. |
| RULE-03 | Довжина повідомлення | Від 10 до 1000 символів. UI прибирає крайні пробіли, API рахує їх. | Рішення не погоджено. |
| RULE-04 | Обов’язковість телефону | Форма й обидва API приймають звернення з порожнім або відсутнім номером. | Для надсилання контактної форми потрібен номер телефону. |
| RULE-05 | Формат заповненого телефону | Початковий `0` форма трактує як український номер. Перед надсиланням прибирає маску. API приймає `+` і 10–15 цифр. | Рішення не погоджено. Q-09. |
| RULE-06 | Згода на обробку даних | Прапорець спочатку знятий. Для надсилання через UI його треба встановити. У запиті та серверній перевірці поле згоди відсутнє. | Рішення не погоджено. Q-07. |
| RULE-07 | Мова підказок і помилок | Підписи й підказки доступні українською та англійською. Типова мова — українська. API повертає англомовні помилки. | Рішення не погоджено. Q-02. |
| RULE-08 | Момент появи помилки або підказки | Поля перевіряються під час введення. При досягненні ліміту імені або повідомлення підказка зникає через три секунди. | Рішення не погоджено. |
| RULE-09 | Результат після помилки надсилання | Помилка запиту потрапляє в консоль. Форма може показати підтвердження навіть після відмови API. Закриття підтвердження очищає форму. | Рішення не погоджено. Q-04. |
| RULE-10 | Додавання документа | Вибір, передавання й збереження файлу відсутні. | Відвідувач може додати CV або портфоліо. |
| RULE-11 | Дані у сповіщенні | Шаблони додають телефон, коли його передано. Інформація про вкладення відсутня. | Сповіщення має відображати нове поле. Точний вміст потребує уточнення. |
| RULE-12 | Фактичне надсилання листа | Обидва обробники повертають відповідь без виклику поштового сервісу. Сам сервіс працює як заглушка. | Рішення не погоджено. Q-03, Q-04. |
| RULE-13 | Збереження звернення | Обробники повертають дані заявнику. Запису звернення в базу в цьому потоці немає. | Рішення не погоджено. Q-03, Q-06. |
| RULE-14 | Доступність форми під час надсилання | Поля, прапорець і кнопка доступні до надсилання. Поки триває запит, вони заблоковані. Після завершення запиту блокування знімається. | Рішення не погоджено. |
| RULE-15 | Неповний номер і очищення поля | Нерозпізнаний код або занадто короткий номер для відомої країни блокує надсилання. Повне очищення поля скидає помилку. | Рішення не погоджено. Q-09. |
| RULE-16 | Відповідь на помилки запиту | При помилках полів обидва API повертають 400 і перелік помилок. При помилці читання JSON обробник collaboration повертає 500 із загальним повідомленням. У contact цей виняток передається фреймворку. | Рішення не погоджено. Q-04. |
| RULE-17 | Додаткові поля у відповіді | Для коректного звернення додаткові поля JSON повертаються разом із перевіреними. Назва файлу в такій відповіді не свідчить про його завантаження. | Рішення не погоджено. |
| RULE-18 | Тип форми в поштовому шаблоні | Якщо тип форми пропущено, генератор використовує Contact у заголовку й тексті шаблону. | Рішення не погоджено. |

## 5. Обмеження й залежності

| ID | Обмеження або залежність | Вплив на перевірку |
| --- | --- | --- |
| CON-01 | Лише PDF — умова вправи. Точний максимальний розмір одного PDF у байтах потребує погодження. | У граничних перевірках AC-05, AC-06, AC-19 використовується L до відповіді на Q-01. |
| CON-02 | Макет допускає 7–15 цифр після плюса, сервер — 10–15. | Очікуваний результат для номерів із 7–9 цифрами визначить Q-22. |
| DEP-01 | Одержувачі, видимість телефону та спосіб передавання PDF залежать від Q-03 і Q-05. | До рішення перевірки приватного доступу умовні. |
| DEP-02 | Зміст згоди, подія початку зберігання та перелік копій потребують рішення за Q-06 і Q-07. | Без відповіді не можна погодити перевірки доступу та видалення як остаточні. |
| DEP-03 | Навчальний потік повертає відповідь без доставки листа. За аналізом Дня 3–4 відмова запиту може призвести до хибного підтвердження. | Демонстрація успіху на екрані та отримання листа в скриньці потребують окремих доказів (AC-10, AC-25, AC-28). |
| DEP-04 | У формі є українські й англійські тексти. Запропоновані формулювання помилок потребують погодження за Q-02. | Для AC-11 умову й мову звіряють із таблицею текстів у розділі 8. |

## 6. Припущення

Ідентифікатори ASM надано припущенням, які раніше були записані текстом. Значення зі статусом Invented у Дні 2 залишаються непідтвердженими.

| ID | Припущення | Як перевірити | У кого уточнити |
| --- | --- | --- | --- |
| ASM-01 | Навчальне припущення Дня 2: за перші 30 днів після запуску щонайменше 80% усіх контактних звернень отримують першу змістовну відповідь без повторного запиту на телефон чи матеріали. | Погодити мету та метрику з Фундацією. | Фундація, роль не визначено |
| ASM-02 | 5 МБ включно — робоче припущення для одного PDF. | Уточнити точне L у байтах за Q-01. | Роль із Q-01 |
| ASM-03 | Запропоновано обмежити доступ до телефону та PDF працівниками, які опрацьовують звернення, і показувати у сповіщенні наявність файлу та приватне посилання. | Узгодити формат доставки, ролі та видимість за Q-03 і Q-05. | Ролі з Q-03 і Q-05 |
| ASM-04 | Навчальний строк видалення телефону та PDF — 90 днів після закриття звернення. | Погодити строк, подію закриття і всі копії за Q-06. | Роль із Q-06 |
| ASM-05 | Макет показує заповнені поля та PDF після помилки. Збереження саме тих даних і документа, які ввів відвідувач, потребує перевірки. | Узгодити поведінку за Q-04. AC-24 стосується значень полів, долю PDF перевіряти окремо. | Роль із Q-04 |

## 7. Відкриті питання

**День 2.** Формулювання, зв’язки, запропоновані ролі й дати перенесено з [переліку відкритих питань](contact-form-feature.md#8-відкриті-питання). Наведені дати є пропозиціями для планування.

| ID | Питання | Що блокує | Запропонована роль | Запропонована дата | Зв’язки |
| --- | --- | --- | --- | --- | --- |
| Q-01 | What maximum size should the Foundation accept for one PDF? If the proposed inclusive limit of 5 MB is approved, does that mean 5,000,000 or 5,242,880 bytes? | The form, API and boundary tests need the same last accepted byte. | Product Owner | 2026-11-25 | AC-05, AC-06, AC-19 / RULE-10 |
| Q-02 | Do you approve the proposed English and Ukrainian error texts in AC-11? Should staff notifications follow the visitor’s language or use a fixed language for the Foundation? | The answers determine the expected text for each locale in the form and in the recipient’s mailbox. | Foundation content lead | 2026-11-30 | AC-04, AC-10, AC-11 / RULE-07, RULE-11 |
| Q-03 | Should staff receive the PDF in the email itself or through a private link? If the file is kept outside email, is private server disk or private object storage (blob) approved? | Email-only delivery and link delivery require different storage, access and deletion arrangements. | Foundation service owner, with IT lead | 2026-11-26 | AC-13, AC-17, AC-18 / RULE-10, RULE-11, RULE-13 |
| Q-04 | If the PDF is saved but email delivery fails, should the Foundation keep the request and retry delivery, or discard it and ask the visitor to resubmit? What should the visitor see? | This sets the success condition and determines how to avoid duplicate requests or files left behind after a retry. | Foundation contact-operations lead | 2026-11-27 | AC-10, AC-23, AC-24, AC-25, AC-28 / RULE-09, RULE-12, RULE-13 |
| Q-05 | Which mailbox receives notifications, and who may see the phone number or open the PDF: everyone handling enquiries or only the staff assigned to that submission? | The recipient list and access tests depend on the authorized audience. This also determines whether putting the phone or CV directly in a shared mailbox is acceptable. | Foundation data owner | 2026-11-26 | AC-04, AC-14, AC-18, AC-30 / RULE-11, RULE-13 |
| Q-06 | What retention period applies to the phone and PDF, what event starts it, and which copies must be deleted, including email attachments? If it starts at closure, who marks a request closed and where? | The proposed 90 days after closure is unconfirmed. Without the start event and covered copies, the deletion date cannot be calculated or tested. | Foundation privacy lead | 2026-11-27 | AC-15, AC-31, AC-32 / RULE-10, RULE-13 |
| Q-07 | Does the existing privacy notice cover CV processing? Must the server reject a request without consent, and does the Foundation need a record of that consent? | Today only the browser checks the box. The answer determines the wording, the API contract and whether consent evidence must be stored. | Foundation privacy lead | 2026-11-27 | AC-08, AC-27 / RULE-06, RULE-13, RULE-16 |
| Q-08 | Which email addresses should the contact form accept? For example, should a visitor be able to use `name+cv@example.org`? | The browser and server apply different rules. The answer determines whether a visitor with otherwise valid contact details can complete submission. | Product Owner | 2026-11-30 | AC-04, AC-16 / RULE-02, RULE-16 |
| Q-09 | Is a phone number required only on Contacts? Should partnership enquiries and requests for notes still work without a phone or PDF? | All three flows use the shared form. The answer sets the scope of the new validation and the regression tests. | Product Owner | 2026-11-25 | AC-01, AC-02, AC-16 / RULE-04, RULE-10, RULE-15 |

**Аналіз макета.** Нижче наведено запропоновані ролі. Дати для Q-10–Q-24 — пропозиції для refinement станом на 26.11.2026. Спочатку заплановано уточнення правил полів і безпеки, потім — текстів, взаємодії та операційних залежностей. [Питання за категоріями](figma-contact-form-analysis.md#питання-за-категоріями-з-ai-аналізу)

| ID | Питання | Що блокує | Запропонована роль | Запропонована дата |
| --- | --- | --- | --- | --- |
| Q-10 | [Бізнес-правила] Яку мінімальну довжину повідомлення погоджує Фундація: чи досить п’яти літер у слові «Вітаю»? | Мінімальна довжина повідомлення | Власник продукту | 2026-11-27 |
| Q-11 | [Бізнес-правила] Чи може Фундація обіцяти відповідь протягом двох–трьох робочих днів для всіх звернень, зокрема для запитів до архіву? | Обіцяний строк відповіді | Керівник команди опрацювання звернень | 2026-11-30 |
| Q-12 | [Поведінка UI] Що має залишитися у формі, коли відвідувач змінює мову після введення тексту, вибору PDF і надання згоди? | Збереження чернетки при зміні мови | Власник продукту | 2026-11-30 |
| Q-13 | [Поведінка UI] Якщо відвідувач увів «Тест», а потрібно п’ять символів, у який момент він має дізнатися, що саме заважає надіслати форму? | Пояснення причини помилки | Дизайнер форми | 2026-11-30 |
| Q-14 | [Модель даних] Що означає «Номер звернення» на екрані успіху: чи має кожне окреме звернення отримати власний номер, навіть якщо людина пише вдруге з тієї самої адреси? | Значення номера звернення | Керівник команди опрацювання звернень | 2026-11-30 |
| Q-15 | [Модель даних] Яку мову відповіді слід пов’язати зі зверненням, якщо відвідувач пише українською, а форму надсилає англійською? | Мова відповіді | Керівник команди опрацювання звернень | 2026-11-30 |
| Q-16 | [Інтеграція] У якій системі працівник Фундації має знаходити звернення за номером, який відвідувач бачить у підтвердженні? | Пошук звернення за номером | Відповідальний за системи Фундації | 2026-12-01 |
| Q-17 | [Інтеграція] Якщо відвідувач скористається адресою з екрана помилки й напише напряму, чи потрапить такий лист до тієї самої черги опрацювання, що й звернення з форми? | Обробка прямих звернень | Керівник команди опрацювання звернень | 2026-12-01 |
| Q-18 | [Крайові випадки] Якщо відвідувач перетягне два PDF одночасно, який файл залишиться у формі й за якою ознакою людина зрозуміє свій вибір? | Вибір одного з кількох файлів | Дизайнер форми | 2026-11-30 |
| Q-19 | [Крайові випадки] Якщо зв’язок зникне під час завантаження PDF, до надсилання самого звернення, чи зможе людина прибрати вкладення та надіслати вже введений текст? | Поведінка після обриву завантаження | Власник продукту | 2026-11-30 |
| Q-20 | [Крайові випадки] Чи зараховується кінцевий пробіл у повідомленні «Тест » як п’ятий символ? | Підрахунок довжини повідомлення | Власник продукту | 2026-11-27 |
| Q-21 | [Крайові випадки] Якщо після відправлення зв’язок зник і номер звернення не з’явився, як відвідувач дізнається, чи отримала Фундація першу спробу? | З’ясування результату після втрати зв’язку | Керівник команди опрацювання звернень | 2026-12-01 |
| Q-22 | [Бізнес-правила] Чи має контактна форма приймати міжнародний номер із 7–9 цифрами разом із кодом країни, як допускає макет, чи для всіх номерів потрібні щонайменше 10 цифр, як вимагає сервер сайту? | Допустима довжина міжнародного номера | Власник продукту | 2026-11-27 |

**Питання, додані під час уточнення NFR.** Ролі запропоновано для refinement. Дати відповіді запропоновані для планування й очікують підтвердження власників.

| ID | Питання | Що блокує | Запропонований власник рішення | Запропонована дата |
| --- | --- | --- | --- | --- |
| Q-23 | Що має відбутися зі зверненням і вкладенням, якщо PDF визнано шкідливим або його безпечність не вдалося перевірити? Що бачить відвідувач і чи може він надіслати звернення без файлу? | Остаточні критерії для обох результатів перевірки в NFR-04. | Відповідальний за безпеку Фундації | 2026-11-27 |
| Q-24 | Які критерії доступності Фундація приймає для форми та в яких комбінаціях браузера й екранного читача перевіряти помилки й стани завантаження? | Погодження обсягу приймальної перевірки NFR-05. | Власник продукту Фундації за участю дизайнера | 2026-12-01 |
| Q-25 | Якщо за Q-06 відлік починатиметься із закриття, як визначити дату видалення телефону та PDF для звернення, у якого немає зафіксованої дати закриття? | Крайовий випадок NFR-02. Без рішення немає очікуваної дати для перевірки видалення. | Відповідальний за приватність Фундації (як у Q-06) | Разом із Q-06 |

## 8. Сигнали приймання

Сценарії, передумови та прив’язки до правил перенесено з [критеріїв Дня 2](contact-form-feature.md#6-критерії-приймання). Це заплановані перевірки. Окремо наведено регресійні сценарії, умовні критерії доступу й зберігання та узгоджувані тексти помилок.

Опубліковані англомовні сценарії збережено з їхніми ідентифікаторами. **Given** задає передумову, **When** — дію, **Then** — результат. **L** — граничний розмір одного PDF у байтах після погодження за Q-01. MIME — заявлений тип файлу, наприклад `application/pdf`. [Критерії в issue](https://github.com/denterra/sdd-workshop-DenisT/issues/2#issuecomment-7087437457)

### Контактна форма

Each run uses otherwise valid input and checked consent unless the scenario varies that input. Error names refer to the EN/UK copy table. Run validation examples separately. Block 5 of the workshop specifies PDF as the only accepted file type. This is the source for AC-05, AC-07, AC-20 and AC-21. AC-05, AC-06 and AC-19 can be executed at the exact boundary once L is agreed.

| ID | Given … When … Then … | Related RULE-ID |
|---|---|---|
| AC-01 | **Given** the phone field is empty (untouched or cleared), **When** the visitor submits the contact form, **Then** the required-phone error appears beside the phone field. | RULE-04, RULE-07, RULE-15 |
| AC-02 | **Given** a contact request has a missing, empty or whitespace-only phone field, **When** the API receives it, **Then** the API rejects it with a phone-field validation error. | RULE-04, RULE-16 |
| AC-03 | **Given** a valid PDF is selected and the contact phone field contains the incomplete number `+380 (63) 116-46`, **When** the visitor submits, **Then** the phone-format error appears beside the field. | RULE-05, RULE-15 |
| AC-04 | *Proposed phone disclosure in the notification, pending Q-05.* **Given** the visitor entered `+380 (63) 116-4627` and selected no file, **When** the Foundation opens the resulting contact notification, **Then** the notification contains the submitted name, reply email, normalized phone `+380631164627` and message. | RULE-04, RULE-05, RULE-11, RULE-12 |
| AC-05 | **Given** the visitor selects one genuine PDF declared as `application/pdf` (test sizes: L − 1 and L bytes), **When** they submit the contact form, **Then** the contact submission is accepted. | RULE-10, RULE-16 |
| AC-06 | **Given** the selected PDF is L + 1 bytes, **When** the visitor attempts submission, **Then** the file-size error appears beside the file field. | RULE-07, RULE-10 |
| AC-07 | **Given** the selected file has MIME type `image/png`, **When** the visitor attempts submission, **Then** the PDF-only error appears beside the file field. | RULE-07, RULE-10 |
| AC-08 | **Given** the visitor has selected a valid PDF and left the privacy checkbox unchecked, **When** they attempt submission, **Then** the consent error appears beside the checkbox. | RULE-06, RULE-07, RULE-10 |
| AC-09 | **Given** the visitor submits a contact request with a PDF, **When** the request is pending, **Then** the submit button is disabled. | RULE-10, RULE-14 |
| AC-10 | **Given** the Foundation’s mail service rejects the send attempt, **When** the form receives the failure response, **Then** the form displays the send-failure message for the active language. | RULE-07, RULE-09, RULE-12, RULE-16 |
| AC-11 | **Given** the Contacts page is open in EN or UK, **When** a condition in the copy table occurs, **Then** the visible error matches the entry for that condition and language. | RULE-07, RULE-08, RULE-16 |
| AC-16 | **Given** the contact request has a valid phone and no file, **When** the visitor submits it, **Then** the form confirms successful submission. | RULE-04, RULE-09, RULE-10 |
| AC-17 | **Given** a contact request has a phone and no file, **When** the Foundation opens its notification, **Then** the message offers no document to download. | RULE-10, RULE-11 |
| AC-18 | **Given** a contact request includes an accepted PDF, **When** the recipient opens the document from the notification, **Then** the retrieved file is byte-for-byte identical to the submitted PDF. | RULE-10, RULE-11, RULE-12 |
| AC-19 | **Given** a direct contact request carries a PDF of L + 1 bytes, **When** the API receives it, **Then** the API rejects it with a file-size validation error. | RULE-10, RULE-16 |
| AC-20 | **Given** a direct contact request declares an attachment as `image/png`, **When** the API receives it, **Then** the API rejects it with a file-type validation error. | RULE-10, RULE-16 |
| AC-21 | **Given** a plain-text file is named `cv.pdf` and declared as `application/pdf`, **When** the contact API receives it, **Then** the API rejects it with a file-type validation error. | RULE-10, RULE-16 |
| AC-22 | **Given** one contact request is already pending, **When** the visitor clicks submit again, **Then** the submission request count stays at one. | RULE-10, RULE-14 |
| AC-23 | *Proposed retry behavior, pending Q-04.* **Given** a contact submission with a valid phone fails, **When** the visitor returns to the form, **Then** the submit button is available for retry. | RULE-09, RULE-14 |
| AC-24 | *Proposed field retention after failure, pending Q-04.* **Given** a contact submission with a valid phone fails, **When** the visitor returns to the form, **Then** the name, email, phone and message fields still contain their submitted values. | RULE-04, RULE-09 |
| AC-25 | **Given** the mail service has rejected the contact request, **When** the form receives the failure response, **Then** the success confirmation remains hidden. | RULE-09, RULE-12 |
| AC-26 | **Given** a contact submission has a missing phone or invalid attachment, **When** the API rejects it, **Then** the test mailbox receives zero notifications for that submission. | RULE-04, RULE-10, RULE-12, RULE-16 |
| AC-27 | **Given** the visitor left the phone field empty, selected an oversized PDF, selected a PNG or left consent unchecked (one case per run), **When** they try to submit the contact form, **Then** the browser makes zero submission requests. | RULE-04, RULE-06, RULE-10 |
| AC-28 | **Given** the visitor submits one valid contact request with an optional PDF, **When** submission completes successfully, **Then** the test mailbox contains exactly one notification for that request. | RULE-10, RULE-12, RULE-14 |

**AC-12 — архівний, вилучений з активного приймання.** Номер зарезервовано для опублікованих посилань. У підрахунку покриття RULE ↔ AC враховуються лише чинні критерії. Колишній AC-12 об’єднував два наявні сценарії. Їх перевіряють окремо: партнерське звернення без телефону та PDF приймається; форма отримання нот відкриває підтвердження без контактного запиту. Номер залишено за цим записом, щоб зберегти опубліковані посилання на AC-13–AC-33.

### Регресійні критерії поточної поведінки

AC-34–AC-38 фіксують поведінку з [таблиці правил Дня 2](contact-form-feature.md#5-таблиця-правил). Для кожної перевірки решта полів коректні. Для довжини використовуються рядки без крайніх пробілів. Цільові правила для email і повідомлення очікують рішень за Q-08, Q-10 та Q-20.

| ID | Given … When … Then … | Related RULE-ID |
| --- | --- | --- |
| AC-34 | **Дано** імена довжиною 1, 2, 50 і 51 символ. **Коли** сервер перевіряє кожне ім’я окремо, **тоді** 2 і 50 проходять, 1 і 51 дають помилку імені. При введенні одного символу у формі надсилання також блокується. | RULE-01 |
| AC-35 | **Дано** адреси `name@example.org` і `name+cv@example.org`. **Коли** кожну перевіряють через форму та окремо на сервері, **тоді** першу приймають обидві перевірки, а для другої перевірка формату у формі проходить і сервер відхиляє адресу. | RULE-02 |
| AC-36 | **Дано** повідомлення довжиною 9, 10, 1000 і 1001 символ. **Коли** сервер перевіряє кожне окремо, **тоді** 10 і 1000 проходять, 9 і 1001 дають помилку повідомлення. При введенні дев’яти символів у формі надсилання також блокується. | RULE-03 |
| AC-37 | **Дано** коректний запит із додатковим текстовим полем `exampleTag` зі значенням `demo`. **Коли** чинний контактний API приймає запит, **тоді** в повернених даних звернення є `exampleTag` з тим самим значенням. | RULE-17 |
| AC-38 | **Дано** дані для поштового шаблону без поля `formType`. **Коли** сформовано HTML і звичайний текст листа, **тоді** в обох версіях заголовок містить «New Contact Form Submission». | RULE-18 |

### Доступ і зберігання · очікують рішення Фундації

**Invented, не підтверджено.** Confirm the delivery option, access roles, event that marks closure and storage locations before using these criteria for sign-off. Use distinct submission and closure dates in retention tests.

| ID | Given … When … Then … | Related RULE-ID |
|---|---|---|
| AC-13 | **Given** secure-link delivery is agreed and a contact request is accepted, **When** the recipient opens its notification, **Then** the message contains a link to that private submission. | RULE-11, RULE-13 |
| AC-14 | **Given** a private submission contains a phone number, **When** a user opens it, **Then** the visibility of the phone number matches the access matrix below. | RULE-11, RULE-13 |
| AC-15 | **Given** the phone number is present at closure, **When** 90 days have elapsed since the recorded closure time, **Then** the number is absent from the agreed storage locations. | RULE-13 |
| AC-29 | **Given** secure-link delivery is agreed, **When** the recipient opens a notification for a submission with or without a PDF, **Then** the attachment status in the message matches whether the visitor supplied a PDF. | RULE-10, RULE-11 |
| AC-30 | **Given** a PDF is stored for a private submission, **When** a user follows its file link, **Then** the download outcome matches the access matrix below. | RULE-10, RULE-11, RULE-13 |
| AC-31 | **Given** a PDF is present at closure, **When** 90 days have elapsed since the recorded closure time, **Then** the PDF is absent from the agreed storage locations. | RULE-10, RULE-13 |
| AC-32 | **Given** a PDF was accessible through a private link at closure, **When** an authorized handler follows that link 90 days after closure, **Then** the link no longer retrieves the PDF. | RULE-10, RULE-13 |
| AC-33 | **Given** secure-link delivery is agreed and a submission has no PDF, **When** an authorized handler opens the private submission, **Then** the submission offers no file to download. | RULE-10, RULE-13 |

| Test identity | Phone visible (AC-14) | PDF downloadable (AC-30) |
|---|---|---|
| Handler authorized for this submission | Yes | Yes |
| Staff member without handling permission | No | No |
| Signed-out visitor | No | No |

### Тексти помилок для AC-11

| Condition | EN | UK | Copy status |
|---|---|---|---|
| Empty phone | Enter your phone number. | Вкажіть номер телефону. | Proposed |
| Invalid phone format | Check the phone number format | Перевірте формат номера телефону | Existing |
| File too large | Choose a PDF no larger than {limit}. | Виберіть PDF розміром до {limit} включно. | Proposed; {limit} is the localized limit confirmed in Q-01 |
| Wrong file type | Choose a PDF file. | Виберіть файл у форматі PDF. | Proposed |
| Privacy consent unchecked | You must agree to continue | Щоб продовжити, потрібно дати згоду | Existing |
| Send failed | Your request could not be sent. Please try again. | Не вдалося надіслати звернення. Спробуйте ще раз. | Proposed |

## 9. Простежуваність

[Повна матриця від запиту до ідеї тесту та статусу](#повна-матриця-простежуваності) наведена наприкінці специфікації.

[Запит Фундації № 2](https://github.com/denterra/sdd-workshop-DenisT/issues/2) → правила розділу 4 → критерії розділу 8. Відповідності Дня 2 доповнено регресійними критеріями AC-34–AC-38. Для правил без прямого речення в issue у таблиці наведено джерело чинної поведінки.

Дизайн — [кадр контактної форми](https://www.figma.com/design/PA4DkifDmVNTCLJ5J1P2YM/Contact-Form?node-id=1-3). У [розборі компонентів і станів](figma-contact-form-analysis.md#компоненти-й-повторне-використання) зазначено, що показано у вибраному кадрі та що відомо з демонстраційних станів. Доставку, права доступу й видалення перевіряють поза кадром.

| Правило | Чинні AC | Що перевірити |
| --- | --- | --- |
| RULE-01 | AC-34 | Перевірити нижню й верхню межі довжини імені. [ContactForm.tsx:58](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/components/forms/contact-form/ContactForm.tsx#L58) |
| RULE-02 | AC-35 | Звичайна адреса та адреса з плюсом. Цільову поведінку уточнює Q-08. [ContactForm.tsx:64](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/components/forms/contact-form/ContactForm.tsx#L64) |
| RULE-03 | AC-36 | Межі повідомлення з регресійної таблиці. Q-10 і Q-20 залишаються відкритими. [ContactForm.tsx:66](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/components/forms/contact-form/ContactForm.tsx#L66) |
| RULE-04 | AC-01, AC-02, AC-04, AC-16, AC-24, AC-26, AC-27 | Порожній, очищений або відсутній номер. |
| RULE-05 | AC-03, AC-04 | Неповний номер і номер у сповіщенні за умови погодження Q-05. [useHandlePhoneInput.ts:73](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/hooks/use-handle-phone-input/useHandlePhoneInput.ts#L73) |
| RULE-06 | AC-08, AC-27 | Знята згода, видима причина й відсутність запиту. [ContactForm.tsx:72](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/components/forms/contact-form/ContactForm.tsx#L72) |
| RULE-07 | AC-01, AC-06, AC-07, AC-08, AC-10, AC-11 | Перевірити помилки в обох мовах за таблицею текстів. [routing.ts:4](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/i18n/routing.ts#L4) |
| RULE-08 | AC-11 | Порівняти текст під час появи відповідної умови. [ContactForm.tsx:88](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/components/forms/contact-form/ContactForm.tsx#L88) |
| RULE-09 | AC-10, AC-16, AC-23, AC-24, AC-25 | Окремо перевірити успіх і відмову пошти. [OfferCollaborationForm.tsx:45](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/components/forms/offer-collaboration-form/OfferCollaborationForm.tsx#L45) |
| RULE-10 | AC-05, AC-06, AC-07, AC-08, AC-09, AC-16, AC-17, AC-18, AC-19, AC-20, AC-21, AC-22, AC-26, AC-27, AC-28, AC-29, AC-30, AC-31, AC-32, AC-33 | Звернення з PDF та без нього, розмір, тип і вміст файлу. |
| RULE-11 | AC-04, AC-17, AC-18, AC-13, AC-14, AC-29, AC-30 | Перевірити в одержувача сповіщення та сам документ. |
| RULE-12 | AC-04, AC-10, AC-18, AC-25, AC-26, AC-28 | Розрізняти прийняття листа поштою і фактичне отримання. [route.ts:17](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/api/collaboration/route.ts#L17) |
| RULE-13 | AC-13, AC-14, AC-15, AC-30, AC-31, AC-32, AC-33 | Умовні перевірки доступу та видалення після погодження. [route.ts:17](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/api/collaboration/route.ts#L17) |
| RULE-14 | AC-09, AC-22, AC-23, AC-28 | Повторне натискання під час запиту та стан кнопки після збою. [ContactForm.tsx:36](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/components/forms/contact-form/ContactForm.tsx#L36) |
| RULE-15 | AC-01, AC-03 | Порожній і неповний номер. Суміжні сценарії — регресійний запис AC-12. [useHandlePhoneInput.ts:152](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/shared/hooks/use-handle-phone-input/useHandlePhoneInput.ts#L152) |
| RULE-16 | AC-02, AC-05, AC-10, AC-11, AC-19, AC-20, AC-21, AC-26 | Відхилення хибного запиту, відсутність листа після відмови. [validateContactData.ts:13](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/lib/utils/validateContactData.ts#L13) |
| RULE-17 | AC-37 | Повернення назви файлу не підтверджує передавання документа. [validateRequestData.ts:7](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/lib/utils/validateRequestData.ts#L7) |
| RULE-18 | AC-38 | Перевірити заголовок в HTML і текстовій версії листа без заданого типу форми. [emails.ts:85](https://github.com/denterra/sdd-workshop-DenisT/blob/36a7e8b5fe629b4a1306bf93a037c12c02264608/app/services/email/emails/emails.ts#L85) |

---

## 10. Планування MVP / PoC (необов'язково, перед повною розробкою)

Використовуйте, коли команді потрібен **перевірений зріз** до зобов'язань на повну поставку. Деталі в `mvp-poc-slice-template.md`.

| Поле | Значення |
|------|----------|
| Тип зрізу | PoC / MVP / Spike |
| Гіпотеза (1 речення) | |
| Найменший перевірюваний зріз | |
| Цільове вікно | напр. 2 тижні |

### Межі лише для зрізу

| ID | У зрізі | Відкладено на повну фічу |
|----|---------|--------------------------|
| SLICE-01 | | |

### Сигнали успіху: PoC проти продакшену

| ID | Сигнал | Поріг PoC | Поріг продакшену |
|----|--------|-----------|------------------|
| SIG-01 | | | |

**Достатньо для refinement:** у зрізу PoC/MVP є **явні рядки OUT**; гіпотеза спростовна; зобов'язання для продакшену (якщо є) названі у стовпці SIG.

---

## Нефункціональні вимоги (NFR)

У матеріалах Дня 2 ці вимоги описано в правилах, критеріях і питаннях без окремих NFR-ID. Нижче їм присвоєно номери для простежуваності. У [навчальній розмові Дня 2](https://github.com/denterra/sdd-workshop-DenisT/issues/2#issuecomment-7009184390) роль представника Фундації виконував ШІ. Запропоновані ролі доступу та строки зберігання очікують рішення Фундації.

| ID | Категорія | Вимога й статус | Як перевірити | Джерело / відкрите рішення |
| --- | --- | --- | --- | --- |
| NFR-01 | Приватність і доступ | Форма збирає ім’я, email, телефон і повідомлення для опрацювання звернення та відповіді. Відвідувач за бажанням додає CV або портфоліо. За пропозицією Дня 2 телефон і PDF доступні лише працівникам, уповноваженим опрацьовувати звернення. Коло одержувачів, склад сповіщення та обсяг згоди потребують погодження. | Подати звернення з PDF і без нього. Після рішення про ролі звірити вміст сповіщення та окремо перевірити видимість телефону й доступ до PDF для уповноваженого працівника, іншого працівника та відвідувача без входу. | RULE-04, RULE-06, RULE-10, RULE-11; умовні AC-04, AC-14, AC-30; ASM-03, Q-03, Q-05, Q-07. |
| NFR-02 | Зберігання персональних даних | Для телефону та PDF потрібен погоджений строк. Пропозиція Дня 2 — видалення через 90 днів після закриття звернення. Хто фіксує закриття та які копії охоплює видалення, включно з поштовими вкладеннями, визначає Q-06. Випадок без зафіксованої дати закриття уточнює Q-25. | Після погодження використати різні дати подання й закриття. На межі строку перевірити телефон і PDF в усіх погоджених місцях. Для варіанта з приватним посиланням перевірити також доступ за попереднім посиланням. | RULE-13, ASM-04; умовні AC-15, AC-31, AC-32; Q-03, Q-06, Q-25. |
| NFR-03 | Безпека завантаження: тип і вміст | За умовою вправи приймається PDF. Сервер перевіряє заявлений тип, фактичний вміст та розмір. Межа L у байтах потребує погодження; 5 МБ — робоче припущення. | Справжній PDF до L включно проходить перевірку. Файл на L + 1 байт, PNG та текстовий файл із назвою `cv.pdf` і заявленим типом `application/pdf` відхиляються. У скриньці Фундації немає сповіщення за відхиленим зверненням. | RULE-10, RULE-16, AC-05–AC-07, AC-19–AC-21, AC-26; ASM-02, Q-01. |
| NFR-04 | Безпека завантаження: шкідливий PDF | **Пропозиція на погодження.** Працівник не повинен отримати або відкрити через сповіщення PDF, визначений як шкідливий. Доля самого звернення, текст для відвідувача та поведінка за недоступної перевірки залишаються відкритими. | На безпечному тестовому файлі змоделювати результат «виявлено загрозу». Перевірити, що файл недоступний одержувачу як вкладення і за посиланням. Окремо відтворити результат «перевірку не завершено» й звірити з рішенням за Q-23. | Безпека шкідливих вкладень — завдання блоку 5, кроку 3. Рішення Фундації за День 2 відсутнє. Q-23. |
| NFR-05 | Доступність | **Запропоновані критерії за аналізом макета.** Відвідувач може заповнити поля, вибрати PDF без перетягування, надати згоду та надіслати форму лише клавіатурою. Фокус видимий. Екранний читач повідомляє поле, причину помилки й спосіб виправлення, а також завершення завантаження та результат надсилання. | Пройти форму клавіатурою з PDF і без нього. Скасувати вибір файлу та перевірити повернення фокуса. З екранним читачем відтворити помилку телефону й завеликий PDF. Для звичайного тексту перевірити в браузері запропонований контраст 4,5:1; у прикладах імені та email на знімку виміряно 3,17:1. | [Перевірки доступності макета](figma-contact-form-analysis.md#доступність); погодження критеріїв і тестових середовищ — Q-24. |
| NFR-06 | Локалізація | Підписи, підказки й помилки контактної форми мають відповідати активній мові сторінки (UK або EN). Чинні та запропоновані формулювання наведено в таблиці для AC-11. Мова сповіщення працівнику й мова відповіді відвідувачеві потребують окремих рішень. | В обох мовах відтворити кожну умову таблиці AC-11 та звірити видимий текст із відповідною колонкою після погодження. Для помилки надсилання перевірити, що відвідувач бачить повідомлення мовою сторінки. | RULE-07, AC-10, AC-11; Q-02 (тексти й мова сповіщення), Q-15 (мова відповіді). |

---

## Доменні розширення

### Приватність / персональні дані
| ID | Вимога |
|----|--------|
| | |

### Зберігання й сповіщення (напр. вкладення, листи)
| ID | Вимога |
|----|--------|
| | |

---

## Чекліст refinement

- [ ] Проблему й результат узгоджено із замовником
- [ ] Виключення переглянули зачеплені команди
- [x] Правила мають джерела або позначені як чернетка
- [ ] Сигнали приймання можна показати на демо
- [x] Для всіх відкритих питань зазначено запропонованих власників і дати відповіді

---

## Анти-патерни (видаліть перед поширенням, якщо не потрібні)

- Не вставляйте сюди схему БД, якщо це не обмеження
- Не пишіть піксельних специфікацій UI: посилайтеся на дизайн, коли він готовий
- Не закривайте відкриті питання здогадками: позначайте їх як ASM-##

---

## Повна матриця простежуваності

**Джерело запиту.** S1–S4 — речення з [Original request, issue № 2](https://github.com/denterra/sdd-workshop-DenisT/issues/2) у вихідному порядку.

- **S1** — «We want to tighten up the contact form.»
- **S2** — «Phone number should be mandatory.»
- **S3** — «We should also let visitors attach a document (CV or portfolio) when they write to us.»
- **S4** — «Notifications need to reflect the new field.»

S1 задає загальну мету. Її конкретизують S2 → IN-01 / RULE-04, S3 → IN-02 / RULE-10 та S4 → IN-03 / RULE-11. У колонці Source вказано речення для відповідної частини сценарію. Позначка «Контекст» означає, що речення пояснює тему, але деталь тесту внесено під час аналізу.

**† — немає прямого речення в issue.** Для кожного такого RULE розділ 9 дає посилання на код, за яким у Дні 2 описали чинну поведінку. Погодження цільових змін фіксують окремо. **Draft** — чернетка перевірки. **Open Q-NN** — для остаточного очікування потрібне рішення за вказаним питанням. Запису про погодження повного сценарію з іменем і датою в перевірених матеріалах немає.

Назви станів належать одному [кадру Contact Form][tm-figma]; окремих посилань на дочірні кадри для цих станів немає. Межі спостереження наведено в [аналізі макета](figma-contact-form-analysis.md#компоненти-й-повторне-використання). Кожен тест нижче — ідея перевірки, а не звіт про запуск.

| Запит (Source) | Вимога / правило | AC | Дизайн (кадр Figma) | Ідея тесту | Статус |
| --- | --- | --- | --- | --- | --- |
| S2 | RULE-04, RULE-07†, RULE-15† | AC-01 | [кадр 1:3][tm-figma] · Empty form · телефон | Не торкатися поля, потім окремо ввести й очистити його. В обох випадках при спробі надсилання очікувати помилку біля телефону. | Draft |
| S2 | RULE-04, RULE-16† | AC-02 | Поза кадром · API | Надіслати три запити: без поля телефону, з порожнім рядком і лише з пробілами. Кожен має отримати помилку телефонного поля. | Draft |
| Контекст: S2 | RULE-05†, RULE-15† | AC-03 | [кадр 1:3][tm-figma] · Invalid phone | Вибрати коректний PDF і ввести неповний номер із AC-03. Перевірити, що помилку формату показано саме біля телефону. | Open Q-22 |
| S2, S4 | RULE-04, RULE-05†, RULE-11, RULE-12† | AC-04 | Поза кадром · Пошта | Надіслати форму без PDF із номером у масці. Після рішення про видимість звірити в листі ім’я, email, нормалізований номер і повідомлення. | Open Q-05 |
| S3 | RULE-10, RULE-16† | AC-05 | [кадр 1:3][tm-figma] · Filled valid · PDF | Надіслати справжні PDF розмірами L − 1 і L байтів. Обидва звернення мають бути прийняті. | Open Q-01 |
| S3 | RULE-07†, RULE-10 | AC-06 | [кадр 1:3][tm-figma] · File too large | Вибрати PDF розміру L + 1. Помилка має з’явитися біля поля файлу. | Open Q-01 |
| S3 | RULE-07†, RULE-10 | AC-07 | [кадр 1:3][tm-figma] · Wrong file type | Вибрати PNG. Біля вкладення має з’явитися вимога вибрати PDF. | Draft |
| Контекст: S3 | RULE-06†, RULE-07†, RULE-10 | AC-08 | [кадр 1:3][tm-figma] · Empty form · згода | Вибрати валідний PDF, залишити згоду знятою та спробувати надіслати. Перевірити повідомлення біля прапорця. | Draft |
| Контекст: S3 | RULE-10, RULE-14† | AC-09 | [кадр 1:3][tm-figma] · кнопка. Окремий стан очікування відповіді не показано. | Затримати відповідь на надсилання з PDF. Поки запит триває, кнопка недоступна. | Draft |
| Контекст: S4 | RULE-07†, RULE-09†, RULE-12†, RULE-16† | AC-10 | [кадр 1:3][tm-figma] · Error | Змоделювати відмову пошти. В обох мовах перевірити повідомлення про збій за таблицею AC-11. | Open Q-02 |
| Прямого речення немає | RULE-07†, RULE-08†, RULE-16† | AC-11 | [кадр 1:3][tm-figma] · Invalid phone / File too large / Wrong file type / Error | Для кожної умови з таблиці AC-11 відкрити UK і EN та порівняти фактичний текст із відповідним рядком. | Open Q-02 |
| Контекст: S4 | RULE-11, RULE-13† | AC-13 | Поза кадром · Пошта | Якщо погодять приватне посилання, відкрити лист і перевірити, що посилання веде саме до відповідного звернення. | Open Q-03 |
| Контекст: S4 | RULE-11, RULE-13† | AC-14 | Поза кадром · Права доступу | Для одного звернення перевірити видимість телефону під трьома ідентичностями з таблиці AC-14. | Open Q-05 |
| Прямого речення немає | RULE-13† | AC-15 | Поза кадром · Зберігання | Після погодження строку задати різні дати подання й закриття. Відрахувати строк від закриття й перевірити відсутність номера в усіх погоджених копіях. | Open Q-06 |
| S2, S3 | RULE-04, RULE-09†, RULE-10 | AC-16 | [кадр 1:3][tm-figma] · Success. Окремий стан без PDF відсутній. | Вказати валідний телефон та надіслати без файлу. Перевірити успіх саме для цього звернення. | Draft |
| S3, S4 | RULE-10, RULE-11 | AC-17 | Поза кадром · Пошта | Відкрити сповіщення за зверненням без PDF. У ньому немає документа для завантаження. | Draft |
| S3, S4 | RULE-10, RULE-11, RULE-12† | AC-18 | Поза кадром · Пошта | Надіслати відомий PDF, отримати його зі сповіщення погодженим способом і побайтово порівняти з оригіналом. | Open Q-03 |
| S3 | RULE-10, RULE-16† | AC-19 | Поза кадром · API | Надіслати напряму справжній PDF на один байт понад L. Перевірити відмову через розмір. | Open Q-01 |
| S3 | RULE-10, RULE-16† | AC-20 | Поза кадром · API | Надіслати напряму файл із заявленим типом image/png. Перевірити серверну відмову через тип. | Draft |
| S3 | RULE-10, RULE-16† | AC-21 | Поза кадром · API | Надіслати текстовий файл як cv.pdf із типом application/pdf. Перевірити, що сервер відхиляє вміст. | Draft |
| Контекст: S3 | RULE-10, RULE-14† | AC-22 | [кадр 1:3][tm-figma] · кнопка. Окремий стан очікування відповіді не показано. | Під час першого запиту знову натиснути «Надіслати» й порахувати мережеві запити. Очікується один. | Draft |
| Прямого речення немає | RULE-09†, RULE-14† | AC-23 | [кадр 1:3][tm-figma] · Error | Після відмови повернутися у форму. Якщо погодять повтор, кнопка знову доступна. | Open Q-04 |
| Контекст: S2 | RULE-04, RULE-09† | AC-24 | [кадр 1:3][tm-figma] · Error | Ввести власні ім’я, email, телефон і повідомлення, спричинити відмову й порівняти всі чотири значення після повернення. | Open Q-04 |
| Контекст: S4 | RULE-09†, RULE-12† | AC-25 | [кадр 1:3][tm-figma] · Error / Success | Відхилити відправлення на боці пошти й перевірити, що підтвердження успіху не з’явилося. | Draft |
| S2, S3, S4 | RULE-04, RULE-10, RULE-12†, RULE-16† | AC-26 | Поза кадром · API і пошта | Окремо подати звернення без телефону та з хибним вкладенням. Після відмови перевірити нуль листів у тестовій скриньці для кожного звернення. | Draft |
| S2, S3 | RULE-04, RULE-06†, RULE-10 | AC-27 | [кадр 1:3][tm-figma] · Empty form / File too large / Wrong file type | Провести чотири спроби: порожній телефон, завеликий PDF, PNG та знята згода. Перевірити нуль запитів у браузері для кожної спроби. | Open Q-01 |
| Контекст: S3, S4 | RULE-10, RULE-12†, RULE-14† | AC-28 | Поза кадром · Пошта | Надіслати по одному валідному зверненню з PDF та без нього. Для кожного у скриньці має бути рівно один відповідний лист. | Draft |
| S3, S4 | RULE-10, RULE-11 | AC-29 | Поза кадром · Пошта | Для погодженого варіанта з приватним посиланням порівняти два листи: стан вкладення має відповідати тому, чи додано PDF. | Open Q-03 |
| Контекст: S3, S4 | RULE-10, RULE-11, RULE-13† | AC-30 | Поза кадром · Права доступу | Перевірити посилання на той самий PDF для уповноваженого працівника, іншого працівника й відвідувача без входу за матрицею AC-30. | Open Q-05 |
| Контекст: S3 | RULE-10, RULE-13† | AC-31 | Поза кадром · Зберігання | Після погодження строку перевірити відсутність PDF у визначених копіях, відрахувавши строк від зареєстрованого закриття. | Open Q-06 |
| Контекст: S3 | RULE-10, RULE-13† | AC-32 | Поза кадром · Зберігання | Перевірити доступне уповноваженій особі посилання до спливу строку та неможливість отримати PDF за ним після погодженої межі. | Open Q-06 |
| S3, S4 | RULE-10, RULE-13† | AC-33 | Поза кадром · Приватне звернення | Якщо погодять приватне посилання, відкрити ним звернення без PDF як уповноважений працівник. Завантажувати нічого. | Open Q-03 |
| Прямого речення немає | RULE-01† · ім’я | AC-34 | [кадр 1:3][tm-figma] · Full name | Перевірити імена довжиною 1, 2, 50 та 51 символ за регресійною таблицею розділу 8. | Draft |
| Прямого речення немає | RULE-02† · email | AC-35 | [кадр 1:3][tm-figma] · Email address | Зіставити приймання звичайної адреси та адреси з плюсом у формі й на сервері. | Open Q-08 |
| Прямого речення немає | RULE-03† · повідомлення | AC-36 | [кадр 1:3][tm-figma] · Your message | Відтворити межі 9, 10, 1000 і 1001 символ із регресійної таблиці. Окремо погодити мінімум та облік пробілів. | Open Q-10, Q-20 |
| Прямого речення немає | RULE-17† · додаткові поля відповіді | AC-37 | Поза кадром · API | Надіслати коректні поля та exampleTag зі значенням demo. Звірити повернені ключ і значення. | Draft |
| Прямого речення немає | RULE-18† · тип форми в листі | AC-38 | Поза кадром · поштовий шаблон | Пропустити formType. Порівняти заголовок «New Contact Form Submission» в HTML і звичайному тексті. | Draft |
| Контекст: S2, S3, S4 | NFR-01 · приватність | AC-04, AC-14, AC-30 | Поза кадром · пошта й права доступу | Порівняти дані сповіщення, видимість телефону й доступ до PDF за погодженою роллю. | Open Q-03, Q-05, Q-07 |
| Прямого речення немає | NFR-02 · зберігання | AC-15, AC-31, AC-32 | Поза кадром · зберігання | На погодженій межі окремо перевірити номер, файл та раніше видане посилання. Випадок без дати закриття очікує відповіді на Q-25. | Open Q-06, Q-25 |
| Контекст: S3 | NFR-03 · безпека файлу | AC-05, AC-06, AC-07, AC-19, AC-20, AC-21, AC-26 | [кадр 1:3][tm-figma] · помилки файлу; сервер поза кадром | Перевірити підміну вмісту, MIME та граничні розміри. Для відхиленого звернення перевірити відсутність сповіщення. | Open Q-01 |
| Контекст: S3 | NFR-04 · шкідливий файл | Окремого AC немає | Поза кадром · результат перевірки безпеки | На безпечному зразку змоделювати результати «виявлено загрозу» та «перевірку не завершено». Звірити доступ до файлу й результат звернення з майбутнім рішенням. | Open Q-23 |
| Прямого речення немає | NFR-05 · доступність | Окремого AC немає | [кадр 1:3][tm-figma] · поля, вибір PDF, згода, кнопка | Пройти форму клавіатурою та з екранним читачем, викликати помилки телефону й PDF та перевірити озвучування. | Open Q-24 |
| Прямого речення немає | NFR-06 · мова | AC-10, AC-11 | [кадр 1:3][tm-figma] · EN/УКР | Викликати помилку у двох локалях і звірити текст із погодженою колонкою AC-11. | Open Q-02, Q-15 |

**Покриття.** У матриці 37 чинних AC для 18 RULE. AC-12 залишено в розділі 8 як архівний запис поза активним прийманням. Три правила прямо випливають із запиту; решта 15 мають джерела в коді Дня 2. Для NFR-04 і NFR-05 є ідеї перевірок та відкриті Q-23 і Q-24. Окремі AC-ID для них очікують уточнення.

[tm-figma]: https://www.figma.com/design/PA4DkifDmVNTCLJ5J1P2YM/Contact-Form?node-id=1-3
