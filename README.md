# Widgetis MCP: віджети магазину на Хорошоп через AI-агента

[Widgetis](https://widgetis.com/?utm_source=github&utm_medium=referral&utm_campaign=mcp-repo) робить готові віджети для інтернет-магазинів на Хорошоп (Horoshop): смугу «до безкоштовної доставки залишилось N грн», рекомендації в кошику, акцію 1+1=3, таймери, попапи, значки довіри та інші. Цей MCP-сервер дає AI-агенту (Claude Code, Cursor, VS Code або інший MCP-клієнт з HTTP-транспортом) ті самі дії, що власник магазину робить у кабінеті Widgetis: увімкнути віджет, змінити поріг, колір чи текст.

*English version below.*

```
Ви: «Постав поріг безкоштовної доставки 1500 грн і зміни текст смуги»
Агент ──▶ agents.widgetis.com/mcp ──▶ кабінет Widgetis ──▶ віджет на сайті оновлено
```

## Що вміє

| Інструмент | Що робить |
| --- | --- |
| `get_my_site` | Ваш магазин: домен, чи стоїть скрипт Widgetis на сторінках, які віджети є |
| `describe_widget` | Як працює віджет і які налаштування та тексти можна змінити |
| `get_widget` | Поточний стан віджета: увімкнений чи ні, налаштування, тексти |
| `enable_widget` | Увімкнути віджет на сайті |
| `disable_widget` | Вимкнути віджет (налаштування зберігаються) |
| `update_widget_config` | Змінити налаштування: поріг, кольори, позицію, умови показу |
| `set_widget_translations` | Змінити тексти віджета для однієї мови (ua, ru, en) |

Кожна зміна перевіряється за схемою віджета ще до збереження. Агент не може записати поле, якого немає, або значення поза межами.

## Як підключити

1. Зареєструйтесь на [widgetis.com](https://widgetis.com/?utm_source=github&utm_medium=referral&utm_campaign=mcp-repo) і додайте свій магазин на Хорошоп.
2. У кабінеті відкрийте сторінку віджетів, картка **«Ключ для AI-агента»**, кнопка **«Створити ключ»**. Ключ починається з `wgt_site_` і діє до 90 днів. Ключ доступний на всіх тарифах, і на безкоштовному Free теж.
3. Додайте сервер у свій клієнт.

**Claude Code**

```bash
claude mcp add --scope user --transport http widgetis https://agents.widgetis.com/mcp \
  --header "Authorization: Bearer wgt_site_ВАШ_КЛЮЧ"
```

**Cursor** (`~/.cursor/mcp.json`) та інші клієнти з `mcpServers`:

```json
{
  "mcpServers": {
    "widgetis": {
      "type": "http",
      "url": "https://agents.widgetis.com/mcp",
      "headers": { "Authorization": "Bearer wgt_site_ВАШ_КЛЮЧ" }
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`):

```json
{
  "servers": {
    "widgetis": {
      "type": "http",
      "url": "https://agents.widgetis.com/mcp",
      "headers": { "Authorization": "Bearer wgt_site_ВАШ_КЛЮЧ" }
    }
  }
}
```

Нічого встановлювати не треба: сервер працює у нас. Покрокова інструкція зі скріншотами: [AI-агент для віджетів Хорошоп](https://widgetis.com/blog/ai-agent-dlya-vidzhetiv-horoshop?utm_source=github&utm_medium=referral&utm_campaign=mcp-repo).

## Приклади запитів агенту

- «Які віджети зараз увімкнені на моєму сайті?»
- «Увімкни смугу безкоштовної доставки від 2000 грн, колір зелений»
- «Зміни текст попапу на "Знижка 10% на перше замовлення" для української та російської версій»
- «Вимкни таймер акції, акція закінчилась»

## Безпека

- Ключ діє лише для одного магазину і лише в межах Вашого тарифу: на Free агент вмикає віджет і міняє ті самі кілька налаштувань, що й кабінет, на Pro і Max усі кольори, тексти й пороги.
- Ключ можна відкликати в кабінеті будь-коли, він перестає працювати одразу.
- Сервер не зберігає ключ: кожен запит несе свій ключ і йде лише до API Widgetis.
- Ліміти: 120 запитів на хвилину з одного ключа; після 10 невірних ключів адреса блокується на 10 хвилин.

## Посилання

- Сайт: [widgetis.com](https://widgetis.com/?utm_source=github&utm_medium=referral&utm_campaign=mcp-repo)
- Каталог віджетів: [widgetis.com/widgets](https://widgetis.com/widgets?utm_source=github&utm_medium=referral&utm_campaign=mcp-repo)
- Тарифи: [widgetis.com/pricing](https://widgetis.com/pricing?utm_source=github&utm_medium=referral&utm_campaign=mcp-repo)
- Підтримка: [Telegram @widgetis](https://t.me/widgetis), hello@widgetis.com

---

## English

**Widgetis MCP server** lets an AI agent manage the widgets of an online store on [Horoshop](https://horoshop.ua), a Ukrainian e-commerce platform. [Widgetis](https://widgetis.com/?utm_source=github&utm_medium=referral&utm_campaign=mcp-repo) provides ready-made store widgets: a free-shipping progress bar, cart recommendations, a 1+1=3 promotion, countdown timers, popups, trust badges and more.

The server is hosted at `https://agents.widgetis.com/mcp` (streamable HTTP). It exposes seven tools: `get_my_site`, `describe_widget`, `get_widget`, `enable_widget`, `disable_widget`, `update_widget_config`, `set_widget_translations`. Every write is validated against the widget's JSON Schema before it is saved.

**Connect:** create a key in the Widgetis cabinet (widgets page, card «Ключ для AI-агента», every plan including Free), then:

```bash
claude mcp add --scope user --transport http widgetis https://agents.widgetis.com/mcp \
  --header "Authorization: Bearer wgt_site_YOUR_KEY"
```

A key works for one store only, within its plan, and can be revoked at any time. The server keeps no credentials.

The cabinet, the widgets and the support are in Ukrainian. Questions: hello@widgetis.com or [Telegram @widgetis](https://t.me/widgetis).

## License

The documentation in this repository is published under the MIT License, see [LICENSE](LICENSE).
