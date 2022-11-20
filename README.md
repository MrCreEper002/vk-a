# vka

#### ``Модуль сделана по основам "vk_api" и "vkquick"`` 

Поддерживает python3.10+

Ниже приведен первый пример, как можно - создать команду, отправить сообщение, выполнить метод вк
```python
from vka import ABot, Context

# токен от ГРУППЫ
bot = ABot(token="group_token")

# добавление команды в бота
@bot.command(commands=['привет', 'hi'])
async def hello_world(ctx: Context):
    # чтобы воспользоваться методом вк
    await ctx.api.users.get(user_ids=1)  
    # тоже самое можно так ->  await ctx.api.method("users.get", {"user_ids":1})
    #   (
    #       {
    #           'first_name': 'Павел', 
    #            'id': 1, 
    #            'last_name': 'Дуров', 
    #            'can_access_closed': True, 
    #            'is_closed': False, 
    #            'can_invite_to_chats': False
    #        }
    #   )
    
    # чтобы получить пользователя который написал команду
    user = await ctx.fetch_sender()
    """
        id              - id
        fn              - имя
        ln              - фамилия
        full            - имя фамилия
        @ - [@id|name]  - становится кликабельная 
    """ 

    # отправка сообщения
    await ctx.answer(f'{user:@full}, привет!')
    
async def poke(ctx: Context):
    await ctx.answer('ТЫК!')
    
bot.register_command(poke, commands='тык')

# запуск бота
bot.run()
```

Второй пример

```python
 
from vka import ABot, Context, Keyboard, Button

bot = ABot(token="group_token")


@bot.click_callback(show_snackbar=True)
async def show_snackbar():
    return 'Произошло чудо 🧩'


@bot.command(commands='привет')
async def hello_world(ctx: Context):
    keyboard = Keyboard(
        Button.callback('Мя').positive().on_called(
            show_snackbar
        ),
    )
    await ctx.answer('Нажми на кнопку чтобы произошло чудо 🤖', keyboard=keyboard)


bot.run()
```