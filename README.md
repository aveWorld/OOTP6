Шаблон інтерпретатора пропонує мову сценаріїв, яка дозволяє кінцевим користувачам налаштовувати своє рішення.

Деякі програми настільки складні, що вимагають розширеної конфігурації. Ви можете запропонувати базову мову сценаріїв, яка дозволяє кінцевому користувачеві маніпулювати вашою програмою за допомогою простих інструкцій. Шаблон інтерпретатора вирішує саме цю проблему – створення простої мови сценаріїв.

Певні типи проблем характеризуються мовою. Ця мова описує проблемну область, яка повинна бути добре зрозуміла і чітко визначена. Для реалізації цього потрібно зіставити мову з граматикою. Граматики зазвичай є ієрархічними деревоподібними структурами, які проходять через кілька рівнів, а потім в кінцевому підсумку отримують кінцеві вузли (також звані літералами).

Такі проблеми, виражені у вигляді граматики, можуть бути реалізовані за допомогою шаблону дизайну інтерпретатора.

Сьогодні, якщо вам дійсно потрібен цей тип управління в JavaScript, ймовірно, простіше використовувати генератор коду, такий як ANTLR, який дозволить вам створювати власні інтерпретатори команд на основі граматики, яку ви надаєте.

![image](https://user-images.githubusercontent.com/46648541/227899935-2f409ce9-ca6e-4f0e-a5ff-57092503ccf9.png)


Учасники
#
Об'єктами, що беруть участь в цій закономірності, є:

Клієнт -- У прикладі коду: програма run().
будує (або дається) синтаксичне дерево, що представляє граматику
встановлює початковий контекст
викликає операції інтерпретації

Контекст -- У прикладі коду: Контекст
містить інформацію про стан перекладача

TerminalExpression -- У прикладі коду: Вираз
реалізує операцію інтерпретації, пов'язану з термінальними символами в граматиці
по одному примірнику для кожного термінального виразу в реченні

NonTerminalExpression -- У прикладі код: не використовується
реалізує операцію інтерпретації, пов'язану з нетермінальними символами в граматиці

Метою цього прикладу є побудова інтерпретатора, який переводить римські цифри в десяткові числа: наприклад, XXXVI = 36.

Об'єкт "Контекст" підтримує вхідні дані (римська цифра) та отриманий вихід, коли він аналізується та інтерпретується. Об'єкт виразу представляє вузли в граматичному дереві; Він підтримує метод інтерпретації.

При запуску програми будується просте граматичне дерево, яке потім обробляє римську цифру і переводить її в цифру.

var Context = function (input) {
    this.input = input;
    this.output = 0;
}

Context.prototype = {
    startsWith: function (str) {
        return this.input.substr(0, str.length) === str;
    }
}

var Expression = function (name, one, four, five, nine, multiplier) {
    this.name = name;
    this.one = one;
    this.four = four;
    this.five = five;
    this.nine = nine;
    this.multiplier = multiplier;
}

Expression.prototype = {
    interpret: function (context) {
        if (context.input.length == 0) {
            return;
        }
        else if (context.startsWith(this.nine)) {
            context.output += (9 * this.multiplier);
            context.input = context.input.substr(2);
        }
        else if (context.startsWith(this.four)) {
            context.output += (4 * this.multiplier);
            context.input = context.input.substr(2);
        }
        else if (context.startsWith(this.five)) {
            context.output += (5 * this.multiplier);
            context.input = context.input.substr(1);
        }
        while (context.startsWith(this.one)) {
            context.output += (1 * this.multiplier);
            context.input = context.input.substr(1);
        }
    }
}

function run() {
    var roman = "MCMXXVIII"
    var context = new Context(roman);
    var tree = [];

    tree.push(new Expression("thousand", "M", " ", " ", " ", 1000));
    tree.push(new Expression("hundred", "C", "CD", "D", "CM", 100));
    tree.push(new Expression("ten", "X", "XL", "L", "XC", 10));
    tree.push(new Expression("one", "I", "IV", "V", "IX", 1));

    for (var i = 0, len = tree.length; i < len; i++) {
        tree[i].interpret(context);
    }

    console.log(roman + " = " + context.output);
}
