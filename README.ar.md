# هـايردس (Hiredis)

<div dir=rtl>

[[English]](README.md)

هذه المكتبة عبارة عن ربط بين لغة الأسس و ال Hiredis و التي بدورها عبارة عن مكتبة للتعامل مع ال redis التي تعد مخزن بيانات ضمن الذاكرة يستعمل كقاعدة بيانات أو كذاكرة مخبئية.

## إضافة المكتبة للمشروع

يمكن تثبيت هذه المكتبة باستعمال التعليمات البرمجية التالية:

```
اشمل "مـحا"؛
مـحا.اشمل_حزمة("Alusus/Hiredis@0.1"، "هـايردس.أسس")
```

<div dir=ltr>

```
import "Apm";
Apm.importPackage("Alusus/Hiredis@0.1", "Hiredis.alusus");
```

</div>

## مثال

```
اشمل "مـتم/طـرفية"؛
اشمل "مـتم/نـظام"؛
اشمل "مـحا"؛
مـحا.اشمل_حزمة("Alusus/Hiredis@0.1"، "هـايردس.أسس")

دالة ابدأ {
    استخدم مـتم؛

    // نعريف المتغيرات اللازمة للاتصال و التعامل مع المستودع
    عرف سياق: مؤشر[هـايردس.سـياق] = 0؛
    عرف رد: مؤشر[هـايردس.الـرد]؛

    // اتصل بالمستودع على المنفذ المحدد
    سياق = هـايردس.اتصل("0.0.0.0", 6379)؛

    // اختبر فيما إذا نجح الاتصال أم لا
    إذا سياق == 0 {
        طـرفية.اطبع("فشل الاتصال: فشل إنشاء السياق.\ج")؛
        نـظام.اخرج(1)؛
    } وإلا إذا سياق~محتوى.خطأ != 0 {
        طـرفية.اطبع("فشل الاتصال: %s\ج", سياق~محتوى.نص_الخطأ~مؤشر)؛
        نـظام.اخرج(1)؛
    }

    // foo في المفتاح hello worldتنفيذ أمر وضع قيمة 
    رد = هـايردس.أمر(سياق, "SET %s %s", "foo", "hello world")؛
    طـرفية.اطبع("SET: %s\n", رد~محتوى.نص)؛
    // تحرير الذاكرة التي تم استعمالها من أجل الرد
    هـايردس.حرر(رد)؛

    // foo تنفيذ أمر جلب القيمة المخزنة في المفتاح
    رد = هـايردس.أمر(سياق, "GET foo")؛
    طـرفية.اطبع("GET foo: %s\n", رد~محتوى.نص)؛
    هـايردس.حرر(رد)؛

    // تنفيذ أمر حذف مفتاح من المستودع و ذلك لكي نزيل أي محتوى
    // تم وضعه نتيجة تنفيذ الكود مسبقاً
    رد = هـايردس.أمر(سياق, "DEL numbers")؛
    هـايردس.حرر(رد)؛

    // numbers سنقوم بوضع أربع عناصر في القائمة ضمن المفتاح
    عرف عداد: صحيح؛
    لكل عداد = 0, عداد < 4, عداد++ {
        رد = هـايردس.أمر(سياق, "RPUSH numbers %d", عداد)؛
        هـايردس.حرر(رد)؛
    }

    // مع تحديد المجالLRANGE يمكن جلب عناصر القائمة عن طريق الأمر 
    // و المجال هنا من البداية حتى النهاية التي نرمز لها ب -1
    رد = هـايردس.أمر(سياق, "LRANGE numbers 0 -1")؛
    طـرفية.اطبع("numbers:")؛

    لكل عداد = 0, عداد <  رد~محتوى.عدد_العناصر, عداد++ {
        عرف رد_جزئي: مؤشر[مؤشر[هـايردس.الـرد]]؛
        // ننقل المؤشر إلى المكان المطلوب
        رد_جزئي = رد~محتوى.عناصر + عداد؛
        طـرفية.اطبع(" %s", رد_جزئي~محتوى~محتوى.نص)؛
    }
    طـرفية.اطبع("\n")؛

    هـايردس.حرر(رد)؛

    هـايردس.حرر(سياق)؛

}

ابدأ()؛
```

<div dir=ltr>

```
import "Srl/Console";
import "Srl/System";
import "Apm";
Apm.importPackage("Alusus/Hiredis@0.1", "Hiredis.alusus");

func main {
    use Srl;

    // The variables we need to connect and communicate with redis
    def c: ptr[Hiredis.Context] = 0;
    def reply: ptr[Hiredis.Reply];

    // connect to redis on the specified port
    c = Hiredis.connect("0.0.0.0", 6379);

    // check if the connection succeed or not
    if c == null {
        Console.print("Connection error: can't allocate redis context\n");
        System.exit(1);
    } else if c~cnt.err != 0 {
        Console.print("Connection error: %s\n", c~cnt.errStr~ptr);
        System.exit(1);
    }

    // execute the command SET which put the value `hellp world` into the key foo
    reply = Hiredis.command(c, "SET %s %s", "foo", "hello world");
    Console.print("SET: %s\n", reply~cnt.str);
    // free the memory that we used to hold the reply
    Hiredis.free(reply);

    // Execute the command GET that fetch the value stored in the key `foo`
    reply = Hiredis.command(c, "GET %s", "foo");
    Console.print("GET foo: %s\n", reply~cnt.str);
    Hiredis.free(reply);

    // Execute the command DEL that delete the key `numbers` from the redis
    // we need this to ensure that no old values are still there.
    reply = Hiredis.command(c, "DEL numbers");
    Hiredis.free(reply);

    // Execute the command RPUSH that puts the value to the right of
    // the numbers list
    def i: int;
    for i = 0, i < 4 , ++i {
        reply = Hiredis.command(c, "RPUSH numbers %d", i);
        Hiredis.free(reply);
    }

    // Execute the commandd LRANGE that fetch the list values in the
    // specified range, -1 means the end of the list
    reply = Hiredis.command(c, "LRANGE numbers 0 -1");
    Console.print("numbers: ");
    for i = 0, i < reply~cnt.elementsCount , ++i {
        def p: ptr[ptr[Hiredis.Reply]];
        // move the pointer to the desired location
        p = reply~cnt.elements + i;
        Console.print(" %s", p~cnt~cnt.str);
    }
    Console.print("\n");
    Hiredis.free(reply);

    Hiredis.free(c);

}

main();
```

</div>

## الأصناف و الدوال

### سـياق (Context)

```
صنف سـياق {
    عرف خطأ: صـحيح؛
    عرف نص_الخطأ: مصفوفة[مـحرف، 128]؛
}
```

<div dir=ltr>

```
class Context {
    def err: int; 
    def errStr: array[char, 128];
}
```

</div>

هذا الصنف يتم عن طريقه الاتصال بالمستودع وتنفيذ الأوامر التي نريدها.

#### خطأ (err)

```
عرف خطأ: صـحيح؛
```

<div dir=ltr>

```
def err: int;
```

</div>

يمثل رمز الخطأ، ويكون 0 في حال عدم حدوث خطأ.

#### نص_الخطأ (errStr)

```
عرف نص_الخطأ: مصفوفة[مـحرف، 128]؛
```

<div dir=ltr>

```
def errStr: array[char, 128];
```

</div>

يمثل رسالة الخطأ في حال حدوثه.

### الـرد (Reply)

```
صنف الـرد {
    عرف النوع: صـحيح؛
    عرف عدد_صحيح: صـحيح[64]؛
    عرف طول_النص: صـحيح[64]؛
    عرف نص: مـؤشر[مـحرف]؛
    عرف عدد_العناصر: صـحيح[64]؛
    عرف عناصر: مـؤشر[مـؤشر[الـرد]]؛
}
```

<div dir=ltr>

```
class Reply {
    def tp:  int; /* REDIS_REPLY_* */
    def integer: int[64]; /* The integer when type is REDIS_REPLY_INTEGER */

    def strLen: int[64]; /* Length of string */
    def str: ptr[char]; /* Used for REDIS_REPLY_ERROR, REDIS_REPLY_STRING
                            REDIS_REPLY_VERB, REDIS_REPLY_DOUBLE (in additional to dval),
                            and REDIS_REPLY_BIGNUM. */

    def elementsCount: int[64]; /* number of elements, for REDIS_REPLY_ARRAY */
    def elements: ptr[ptr[Reply]]; /* elements vector for REDIS_REPLY_ARRAY */
}
```

</div>

هذا الصنف يمثل الرد الذي يأتي بعد تنفيذ أمر ما.

#### النوع (tp)

```
عرف النوع: صـحيح؛
```

<div dir=ltr>

```
def tp: int;
```

</div>

يمثل نوع الرد.

#### عدد_صحيح (integer)

```
عرف عدد_صحيح: صـحيح[64]؛
```

<div dir=ltr>

```
def integer: int[64];
```

</div>

يحمل قيمة الرد في حال كان الرد من النمط الصحيح.

#### طول_النص (strLen)

```
عرف طول_النص: صـحيح[64]؛
```

<div dir=ltr>

```
def strLen: int[64];
```

</div>

عدد محارف `نص`.

#### نص (str)

```
عرف نص: مـؤشر[مـحرف]؛
```

<div dir=ltr>

```
def str: ptr[char];
```

</div>

يحمل قيمة الرد في حال كان الرد من النمط النصي.

#### عدد_العناصر (elementsCount)

```
عرف عدد_العناصر: صـحيح[64]؛
```

<div dir=ltr>

```
def elementsCount: int[64];
```

</div>

يحمل عدد العناصر في القائمة في حال كان الرد عبارة عن قائمة من القيم.

#### عناصر (elements)

```
عرف عناصر: مـؤشر[مـؤشر[الـرد]]؛
```

<div dir=ltr>

```
def elements: ptr[ptr[Reply]];
```

</div>

قائمة القيم في حال كان نمط الرد هو قائمة.

### اتصل (connect)

```
@تصدير[redisConnect]
دالة اتصل(مضيف: مؤشر[محرف]، منفذ: صحيح): مؤشر[السياق]؛
```

<div dir=ltr>

```
@expname[redisConnect]
func connect(host: ptr[char], port: int): ptr[Context]
```

</div>

تستعمل هذه الدالة للاتصال بال `redis`. تعيد مؤشراً يؤشر على متغير من النمط `سـياق` (`Context`) في حال نجاح الاتصال وإلا مؤشراً صفرياً (`null`).

* `مضيف` (`host`): عنوان المضيف.
* `منفذ` (`port`): المنفذ الذي تعمل عليه خدمة `redis`.

### أمر (command)

```
@تصدير[redisCommand]
دالة أمر(سياق: مؤشر[السياق]، الصيغة: مؤشر[محرف]، ...أيما): مؤشر[الرد]؛
```

<div dir=ltr>

```
@expname[redisCommand]
func command(c: ptr[Context], format: ptr[char], ... any): ptr[Reply]
```

</div>

تستعمل هذه الدالة لتنفيذ أمر ما على المستودع، مثل وضع قيمة أو جلب قيمة منه.

* `سياق` (`c`): المتغير الذي يحمل معلومات الاتصال.
* `الصيغة` (`format`): الصيغة التي يتبعها الأمر المراد تنفيذه.
* `أيما` (`any`): المعطيات التي تحتاجها الصيغة.

### حرر (free)

```
@تصدير[freeReplyObject]
دالة حرر(رد: مؤشر[الرد])؛

@تصدير[redisFree]
دالة حرر(سياق: مؤشر[السياق])؛
```

<div dir=ltr>

```
@expname[freeReplyObject]
func free(reply: ptr[Reply])

@expname[redisFree]
func free(c: ptr[Context])
```

</div>

تستعمل هذه الدالة لتحرير الذاكرة المُخصصة للكائن المعطى.

* `رد` (`reply`): تحرير الذاكرة المُخصصة لكائن الرد.
* `سياق` (`c`): تحرير الذاكرة المُخصصة لكائن السياق.

## الرخصة

حقوق النشر © 2022 الأسس للبرمجيات (Alusus Software Ltd)

هذا المشروع مرخص بموجب رخصة BSD. راجع ملف `LICENSE` للحصول على التفاصيل.

</div>
