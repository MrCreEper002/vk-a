# vka

#### ``Модуль сделана по основам "vk_api" и "vkquick"`` 

Ниже приведен первый пример, как можно - создать команду, отпарвить сообщение, выполнить метод вк
```python
from vka import Bot, Validator, User

# токен от ГРУППЫ
bot = Bot("group_token")

# добавление команды в бота
@bot.command(names='привет')
async def hello_world(ctx: Validator):
    # чтобы воспользоваться методом вк
    await ctx.api.users.get(user_ids=1)  
    # тоже самое можно так ->  await ctx.api.method("users.get", {"user_ids":1})
    # AttrDict(
    #   {
    #       'response': 
    #           [
    #               {
    #                   'first_name': 'Павел', 
    #                   'id': 1, 
    #                   'last_name': 'Дуров', 
    #                   'can_access_closed': True, 
    #                   'is_closed': False, 
    #                   'can_invite_to_chats': False
    #               }
    #           ]
    #       }
    # )
    
    # чтобы получить пользователя который написал команду
    user = User((await ctx.fetch_sender()).response[0])
    """
        id              - id
        fn              - имя
        ln              - фамилия
        full            - имя фамилия
        @ - [@id|name]  - становится кликабельная 
    """ 

    # отправка сообщения
    await ctx.answer(f'{user:@full}, привет!')

# запуск бота
bot.run(debug=True)
```

Второй пример

```python
 
from vka import Validator, Bot, Keyboard, Button

bot = Bot("group_token")


@bot.click_callback(show_snackbar=True)
async def show_snackbar():
    return 'Произошло чудо 🧩'


@bot.command(names='привет')
async def hello_world(ctx: Validator):
    keyboard = Keyboard(
        Button.callback('Мя').positive().on_called(
            show_snackbar
        ),
    )
    await ctx.answer('Нажми на кнопку чтобы произошло чудо 🤖', keyboard=keyboard)


bot.run()
```

####Если нужно изменить сообщение, то можно использовать метод `transmit` из класса `Validator` (объединены методы `answer` и `edit`).
