# Rufier-Test

from kivy.app import App
from kivy.uix.screenmanager import ScreenManager, Screen
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.label import Label
from kivy.uix.button import Button
from kivy.uix.textinput import TextInput
from kivy.core.window import Window
from kivy.uix.scrollview import ScrollView
from instructions import txt_instruction, txt_test1, txt_test2, txt_test3, txt_sits
from ruffier import test

from kivy.uix.label import Label
from kivy.clock import Clock

from seconds import Seconds
from sits import Sits
from runner import Runner
from kivy.properties import NumericProperty, BooleanProperty
from kivy.uix.button import Button
from kivy.animation import Animation


from kivy.uix.boxlayout import BoxLayout

from kivy.uix.label import Label
from kivy.clock import Clock
from kivy.properties import BooleanProperty


age = 7
name = ""
p1, p2, p3 = 0, 0, 0
def check_int(str_num):
    try:
        return int(str_num)
    except:
        return False


class FirstScreen(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)

        instr = Label(text=txt_instruction)
        lbl1 = Label(text='Введите имя:')
        self.in_name = TextInput(multiline=False)
        lbl2 = Label(text='Введите возраст:')
        self.in_old = TextInput(text='7', multiline=False)
        self.btn = Button(text='Начать', size_hint=(0.3, 0.2), pos_hint={'center_x': 0.5}, background_color=(0, 1, 0, 1))
        self.btn.on_press = self.next

        line1 = BoxLayout(size_hint=(0.8, 0.0), height='30sp')
        line2 = BoxLayout(size_hint=(0.8, 0.0), height='30sp')
        Btn = BoxLayout(orientation='vertical', padding=8, spacing=8)

        line1.add_widget(lbl1)
        line1.add_widget(self.in_name)
        line2.add_widget(lbl2)
        line2.add_widget(self.in_old)
        
        Btn.add_widget(instr)
        Btn.add_widget(line1)
        Btn.add_widget(line2)
        Btn.add_widget(self.btn)
        self.add_widget(Btn)

    def next(self):
        name = self.in_name.text
        age = check_int(self.in_old.text)
        if age == False or age < 7:
            age = 7
            self.in_old.text = str(age)
        else:
            self.manager.current = 'newScreen1'
     
class newScreen1(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.next_screen = False

        instr = Label(text=txt_test1)
        self.sec = Seconds(15)
        self.sec.bind(done=self.sec_finished)

        line = BoxLayout(size_hint=(0.8, 0.0), height='30sp')
        lbresult = Label(text='Введите результат:', halign='right')
        self.in_result = TextInput(text='0', multiline=False)
        self.in_result.set_disabled(True)

        line.add_widget(lbresult)
        line.add_widget(self.in_result)
        self.btn = Button(text='Начать', size_hint=(0.3, 0.4), pos_hint={'center_x': 0.5}, background_color=(0, 1, 0, 1))
        self.btn.on_press = self.next
        Lay = BoxLayout(orientation='vertical', padding=8, spacing=8)
        Lay.add_widget(instr)
        Lay.add_widget(self.sec)
        Lay.add_widget(line)
        Lay.add_widget(self.btn)
        self.add_widget(Lay)

    def sec_finished(self, *args):
        self.next_screen = True
        self.in_result.set_disabled(False)
        self.btn.set_disabled(False)
        self.btn.text = 'Продолжить'

    def next(self):
        if not self.next_screen:
            self.btn.set_disabled(True)
            self.sec.start()
        else:
            global p1
            p1 = check_int(self.in_result.text)
            if p1 == False or p1 <= 0:
                p1 = 0
                self.in_result.text = str(p1)
            else:
                self.manager.current = 'newScreen2'


class newScreen2(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.next_screen = False

        instr = Label(text=txt_sits, size_hint=(0.5, 1))
        self.lbl_sits = Sits(30)
        self.run = Runner(total=30, steptime=1.5, size_hint=(0.4, 1))
        self.run.bind(finished=self.ffinished)

        line = BoxLayout()
        lay = BoxLayout(orientation='vertical', size_hint=(0.3, 1))
        lay.add_widget(self.lbl_sits)
        line.add_widget(instr)
        line.add_widget(lay)
        line.add_widget(self.run)

        self.btn = Button(text='Начать', size_hint=(0.3, 0.2), pos_hint={'center_x': 0.5}, background_color=(0, 1, 0, 1))
        self.btn.on_press = self.next

        box = BoxLayout(orientation='vertical', padding=8, spacing=8)
        box.add_widget(line)
        box.add_widget(self.btn)

        self.add_widget(box)

    def ffinished(self, instance, value):
        self.btn.set_disabled(False)
        self.btn.text = 'Продолжить'
        self.next_screen = True

    def next(self):
        if not self.next_screen:
            self.btn.set_disabled(True)
            self.run.start()
            self.run.bind(value=self.lbl_sits.next)
        else:
            self.manager.current = 'needcreen'

class needcreen(Screen):
    def __init__(self, **kwargs):
        self.next_screen = False

        self.stage = 0
        super().__init__(**kwargs)
        instr = Label(text=txt_test3)
        lin1 = BoxLayout(size_hint=(0.8, 0.0), height='30sp')
        self.lbsec = Seconds(15)
        self.lbsec.bind(done=self.sec_finished)
        self.lbl1 = Label(text='Считайте пульс')

        rresult1 = Label(text='Результат:', halign='right')
        self.in_result1 = TextInput(text='0', multiline=False)
        lin1.add_widget(rresult1)
        lin1.add_widget(self.in_result1)
        lin2 = BoxLayout(size_hint=(0.8, None), height='30sp')
        rresult2 = Label(text='Результат после отдыха:', halign='right')
        self.in_result2 = TextInput(text='0', multiline=False)

        self.in_result1.set_disabled(True)
        self.in_result2.set_disabled(True)

        lin2.add_widget(rresult2)
        lin2.add_widget(self.in_result2)

        self.btn = Button(text='Начать', size_hint=(0.3, 0.5), pos_hint={'center_x': 0.5}, background_color=(0, 1, 0, 1))
        self.btn.on_press = self.next
        Layoutt = BoxLayout(orientation='vertical', padding=8, spacing=8)
        Layoutt.add_widget(instr)
        Layoutt.add_widget(self.lbl1)
        Layoutt.add_widget(self.lbsec)
        Layoutt.add_widget(lin1)
        Layoutt.add_widget(lin2)
        Layoutt.add_widget(self.btn)
        self.add_widget(Layoutt)


    def sec_finished(self, *kwarddss):
        if self.lbsec.done == True:
            if self.stage == 0:
                self.stage = 1
                self.lbl1.text = 'Отдыхайте'
                self.lbsec.restart(30)
                self.in_result1.set_disabled(False)

            elif self.stage == 1:
                self.stage = 2
                self.lbl1.text='Считайте пульс'
                self.lbsec.restart(15)

            elif self.stage == 2:
                self.in_result2.set_disabled(False)
                self.btn.set_disabled(False)
                self.btn.text = 'Завершить'
                self.next_screen = True

    def next(self):
        if not self.next_screen:
            self.btn.set_disabled(True)
            self.lbsec.start()
        else:
            global p2, p3
            p2 = check_int(self.in_result1.text)
            p3 = check_int(self.in_result2.text)

            if p2 == False:
                p2 = 0
                self.in_result1.text = str(p2)

            elif p3 == False:
                p3 = 0
                self.in_result2.text = str(p3)

            else:
                self.manager.current = 'conec'

class conec(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.Btn = BoxLayout(orientation='vertical', padding=8, spacing=8)
        self.instr = Label(text = '')
        self.Btn.add_widget(self.instr)
        self.add_widget(self.Btn)
        self.on_enter = self.before

    def before(self):
        global name
        self.instr.text = name + '\n' + test(p1, p2, p3, age)


class result(App):
    def build(self):
        sm = ScreenManager()
        sm.add_widget(FirstScreen(name='FirstScreen'))
        sm.add_widget(newScreen1(name='newScreen1'))
        sm.add_widget(newScreen2(name='newScreen2'))
        sm.add_widget(needcreen(name='needcreen'))
        sm.add_widget(conec(name='conec'))
        return sm

app = result()
app.run()


txt_instruction = '''
Данное приложение позволит вам с помощью теста Руфье \n провести первичную диагностику вашего здоровья.\n
Проба Руфье представляет собой нагрузочный комплекс, \n предназначенный для оценки работоспособности сердца при физической нагрузке.\n
У испытуемого определяют частоту пульса за 15 секунд.\n
Затем в течение 45 секунд испытуемый выполняет 30 приседаний.\n
После окончания нагрузки пульс подсчитывается вновь: \nчисло пульсаций за первые 15 секунд, 30 секунд отдыха,\n число пульсаций за последние 15 секунд.\n'''


txt_test1 = '''Замерьте пульс за 15 секунд.\n
Результат запишите в соответствующее поле.'''


txt_test2 = '''Выполните 30 приседаний за 45 секунд.\n
Нажмите кнопку "Начать", чтобы запустить счётчик приседаний.\n
Делайте приседания со скоростью счётчика.'''


txt_test3 = '''В течение минуты замерьте пульс два раза:\n
за первые 15 секунд минуты, затем за последние 15 секунд.\n
Результаты запишите в соответствующие поля.'''


txt_sits = 'Выполните 30 приседаний за 45 секунд.'





''' Модуль для расчёта результатов пробы Руфье.


Сумма измерений пульса в трёх попытках (до нагрузки, сразу после и после короткого отдыха)
в идеале должна быть не более 200 ударов в минуту.
Мы предлагаем детям измерять свой пульс на протяжении 15 секунд,
и приводим результат к ударам в минуту умножением на 4:
   S = 4 * (P1 + P2 + P3)
Чем дальше этот результат от идеальных 200 ударов, тем хуже.
Традиционно таблицы даются для величины, делённой на 10.


Индекс Руфье  
   IR = (S - 200) / 10
оценивается по таблице в соответствии с возрастом:
       7-8             9-10                11-12               13-14               15+ (только для подростков!)
отл.    6.4 и менее    4.9 и менее       3.4 и менее         1.9 и менее               0.4 и менее
хор.    6.5 - 11.9     5 - 10.4          3.5 - 8.9           2 - 7.4                   0.5 - 5.9
удовл.  12 - 16.9      10.5 - 15.4       9 - 13.9            7.5 - 12.4                6 - 10.9
слабый  17 - 20.9      15.5 - 19.4       14 - 17.9           12.5 - 16.4               11 - 14.9
неуд.   21 и более     19.5 и более      18 и более          16.5 и более              15 и более


для всех возрастов результат "неуд" отстоит от "слабого" на 4,
тот от "удовлетворительного" на 5, а "хороший" от "уд" - на 5.5


поэтому напишем функцию ruffier_result(r_index, level), которая будет получать
рассчитанный индекс Руфье и уровень "неуд" для возраста тестируемого, и отдавать результат


'''
# здесь задаются строки, с помощью которых изложен результат:
txt_index = "Ваш индекс Руфье: "
txt_workheart = "Работоспособность сердца: "
txt_nodata = '''
нет данных для такого возраста'''
txt_res = []
txt_res.append('''низкая.
Срочно обратитесь к врачу!''')
txt_res.append('''удовлетворительная.
Обратитесь к врачу!''')
txt_res.append('''средняя.
Возможно, стоит дополнительно обследоваться у врача.''')
txt_res.append('''
выше среднего''')
txt_res.append('''
высокая''')


def ruffier_index(P1, P2, P3):
    return (4 * (P1+P2+P3) - 200) / 10


def neud_level(age):
    norm_age = (min(age, 15) - 7) // 2  # каждые 2 года разницы от 7 лет превращаются в единицу - вплоть до 15 лет
    result = 21 - norm_age * 1.5 # умножаем каждые 2 года разницы на 1.5, так распределены уровни в таблице
    return result
  
def ruffier_result(r_index, level):
    if r_index >= level:
        return 0
    level = level - 4 # это не будет выполняться, если мы уже вернули ответ "неуд"
    if r_index >= level:
        return 1
    level = level - 5 # аналогично, попадаем сюда, если уровень как минимум "уд"
    if r_index >= level:
        return 2
    level = level - 5.5 # следующий уровень
    if r_index >= level:
        return 3
    return 4 # здесь оказались, если индекс меньше всех промежуточных уровней, т.е. тестируемый крут.


    def test(P1, P2, P3, age):
        if age < 7:
            return (txt_index + "0", txt_nodata) # тайна сия не для теста сего
        else:
            ruff_index = ruffier_index(P1, P2, P3) # расчет
            result = txt_res[ruffier_result(ruff_index, neud_level(age))] # интерпретация, перевод числового уровня подготовки в текстовые данные
            res = txt_index + str(ruff_index) + '\n' + txt_workheart + result
            return res





class Runner(BoxLayout):
    value = NumericProperty(0) # сколько сделано перемещений
    finished = BooleanProperty(False) # сделаны ли все перемещения


    def __init__(self,
                total=10,  steptime=1, autorepeat=True,
                bcolor=(0.73, 0.15, 0.96, 1),
                btext_inprogress='Приседание',
                **kwargs):


        super().__init__(**kwargs)


        self.total = total
        self.autorepeat = autorepeat
        self.btext_inprogress = btext_inprogress
        self.animation = (Animation(pos_hint={'top': 0.1}, duration=steptime/2)
                        + Animation(pos_hint={'top': 1.0}, duration=steptime/2))
        self.animation.on_progress = self.next
        self.btn = Button(size_hint=(1, 0.1), pos_hint={'top': 1.0}, background_color=bcolor)
        self.add_widget(self.btn)


    def restart(self, total):
        self.total = total
        self.start()


    def start(self):
        self.value = 0
        self.finished = False
        self.btn.text = self.btext_inprogress
        if self.autorepeat:
            self.animation.repeat = True
        self.animation.start(self.btn)


    def next(self, widget, step):
        if step == 1.0:
            self.value += 1
            if self.value >= self.total:
                self.animation.repeat = False
                self.finished = True





class Seconds(Label):
    done = BooleanProperty(False)
  
    def __init__(self, total, **kwargs):
        self.done = False
        self.current = 0
        self.total = total
        my_text = "Прошло секунд: " + str(self.current)
        super().__init__(text=my_text)


    def restart(self, total, **kwargs):
        self.done = False
        self.total = total
        self.current = 0
        self.text = "Прошло секунд: " + str(self.current)
        self.start()


    def start(self):
        Clock.schedule_interval(self.change, 1)


    def change(self, dt):
        self.current += 1
        self.text = "Прошло секунд: " + str(self.current)
        if self.current >= self.total:
            self.done = True
            return False





class Sits(Label):
    def __init__(self, total, **kwargs):
        self.current = 0
        self.total = total
        my_text = "Осталось приседаний: " + str(self.total)
        super().__init__(text=my_text, **kwargs)


    def next(self, *args):
        self.current += 1
        remain = max(0, self.total - self.current)
        my_text = "Осталось приседаний: " + str(remain)
        self.text=my_text
