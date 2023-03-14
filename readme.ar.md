# Hiredis
[[English]](readme.md)

هذه المكتبة عبارة عن ربط بين لغة الأسس و ال Hiredis و التي بدورها عبارة عن مكتبة للتعامل مع ال redis التي تعد مخزن بيانات ضمن الذاكرة يستعمل كقاعدة بيانات أو كذاكرة مخبئية.

## إضافة المكتبة للمشروع

يمكن تثبيت هذه المكتبة باستعمال التعليمات البرمجية التالية:
```
import "Apm";
Apm.importFile("Alusus/Hiredis");
```

## مثال

```
اشمل "مـتم/طـرفية"؛
اشمل "مـتم/نـظام"؛
اشمل "مـحا"؛
مـحا.اشمل_ملف("Alusus/Hiredis"، "هـايردس.أسس")؛

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
```
import "Srl/Console";
import "Srl/System";
import "Apm";
Apm.importFile("Alusus/Hiredis");

func main {
    use Srl;

    // نعريف المتغيرات اللازمة للاتصال و التعامل مع المستودع
    def c: ptr[Hiredis.Context] = 0;
    def reply: ptr[Hiredis.Reply];

    // اتصل بالمستودع على المنفذ المحدد
    c = Hiredis.connect("0.0.0.0", 6379);

    // اختبر فيما إذا نجح الاتصال أم لا
    if c == null {
        Console.print("Connection error: can't allocate redis context\n");
        System.exit(1);
    } else if c~cnt.err != 0 {
        Console.print("Connection error: %s\n", c~cnt.errStr~ptr);
        System.exit(1);
    }

    // foo في المفتاح hello worldتنفيذ أمر وضع قيمة 
    reply = Hiredis.command(c, "SET %s %s", "foo", "hello world");
    Console.print("SET: %s\n", reply~cnt.str);
    // تحرير الذاكرة التي تم استعمالها من أجل الرد
    Hiredis.free(reply);

    // foo تنفيذ أمر جلب القيمة المخزنة في المفتاح
    reply = Hiredis.command(c, "GET %s", "foo");
    Console.print("GET foo: %s\n", reply~cnt.str);
    Hiredis.free(reply);

    // تنفيذ أمر حذف مفتاح من المستودع و ذلك لكي نزيل أي محتوى
    // تم وضعه نتيجة تنفيذ الكود مسبقاً
    reply = Hiredis.command(c, "DEL numbers");
    Hiredis.free(reply);

    // numbers سنقوم بوضع أربع عناصر في القائمة ضمن المفتاح
    def i: int;
    for i = 0, i < 4 , ++i {
        reply = Hiredis.command(c, "RPUSH numbers %d", i);
        Hiredis.free(reply);
    }

    // مع تحديد المجالLRANGE يمكن جلب عناصر القائمة عن طريق الأمر 
    // و المجال هنا من البداية حتى النهاية التي نرمز لها ب -1
    reply = Hiredis.command(c, "LRANGE numbers 0 -1");
    Console.print("numbers: ");
    for i = 0, i < reply~cnt.elementsCount , ++i {
        def p: ptr[ptr[Hiredis.Reply]];
        // ننقل المؤشر إلى المكان المطلوب
        p = reply~cnt.elements + i;
        Console.print(" %s", p~cnt~cnt.str);
    }
    Console.print("\n");
    Hiredis.free(reply);

    Hiredis.free(c);

}

main();
```

## الأصناف و الدوال

### سـياق (Context)

```
class Context {
    def err: int; /* Error flags, 0 when there is no error */
    def errStr: array[char, 128]; /* String representation of error when applicable */
};
```
```
صنف سـياق {
    عرف خطأ: صـحيح؛
    عرف نص_الخطأ: مصفوفة[مـحرف، 128]؛
}
```
هذا الصنف يتم عن طريقه الاتصال بالمستودع وتنفيذ الأوامر التي نريدها. يمثل المتغير err رمز الخطأ، و يكون 0 في حال عدم
حدوث خطأK أما errStr فيمثل رسالة الخطأ في حال حدوثه.

### الـرد (Reply)

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
};
```
```
صنف الـرد {
    عرف عدد_صحيح: صحيح؛
    عرف طول_النص: صحيح[64]؛
    عرف نص: مؤشر[محرف]؛
    عرف عدد_العناصر: صحيح[64]؛
    عرف عناصر: مؤشر[مؤشر[الرد]]؛
}
```
هذا الصنف يمثل الرد الذي يأتي بعد تنفيذ أمر ما.

يمثل tp نمط الرد.

بينما integer يحمل قيمة الرد في حال كان الرد من النمط الصحيح.

و str يحمل قيمة الرد في حال كان الرد من النمط النصي.

أما elementsCount فيحمل عدد العناصر في القائمة في حال كان الرد عبارة عن قائمة من القيم.

و يحمل elements قائمة القيم في حال كان نمط الرد هو قائمة.

### اتصل (connect)

```
@expname[redisConnect]
func connect(host: ptr[char], port: int): ptr[Context];
```
```
عرف اتصل(مضيف: مؤشر[محرف]، منفذ: صحيح): مؤشر[السياق]؛
```
تستعمل هذا الدالة للاتصال بال redis  و يجب أن نمرر له عنوان المضيف و المنفذ التي تعمل عليه هذه الخدمة.
و تعيد هذا الدالة مؤشر يؤشر على متغير من النمط Context في حال نجاح الاتصال و إلا مؤشر يؤشر على null.

### أمر (command)

```
@expname[redisCommand]
func command(c: ptr[Context], format: ptr[char], ... any): ptr[Reply];
```
```
عرف أمر(سياق: مؤشر[السياق]، الصيغة: مؤشر[محرف]، ...أي وسيط آخر): مؤشر[الرد]؛
```
تستعمل هذه الدالة لتنفيذ أمر ما على المستودع، مثل وضع قيمة أو جلب قيمة منه، و يجب تمرير مؤشر من الصنف Context يمثل المؤشر الذي يحمل معلومات الاتصال، بالإضافة إلى الأمر المراد تنفيذه.

### حرر (free)

```
@expname[freeReplyObject]
func free(reply: ptr[Reply]);
```
```
عرف حرر(رد: مؤشر[الرد])؛
```
تستعمل هذه الدالة لتحرير الذاكرة التي تم يؤشر عليها متحول من الصنف Reply.
```
@expname[redisFree]
func free(c: ptr[Context]);
```
```
عرف حرر(سياق: مؤشر[السياق])؛
```
تستعمل هذه الدالة لتحرير الذاكرة التي تم يؤشر عليها متحول من الصنف Context.


