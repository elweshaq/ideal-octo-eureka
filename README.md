#ELWESHAQSMS-BOT
📜 برومت/مواصفات تنفيذية – بوت الأرقام (الأرقام يضيفها الأدمن، والـ API فقط لاستخراج أكواد التحقق)

> وثيقة مواصفات كاملة قابلة للتنفيذ من قِبل AI Agent أو مطوّر.
اللغة: العربية – الواجهة على تيليجرام فقط، لا موقع.
المعمارية المقترحة: Python (aiogram أو python-telegram-bot) + قاعدة بيانات (PostgreSQL/MySQL/SQLite).
جميع الأزرار Inline Keyboard ما لم يُذكر غير ذلك.




---

1) تدفّق المستخدم (User Flow)

1. /start

رسالة ترحيب.

قائمة الخدمات المتاحة (أزرار إنلاين):
WhatsApp | Telegram | Facebook | Instagram | Twitter | ...

زر إضافي: 🆓 تجميع رصيد مجاني (يجمع رصيد مقابل الاشتراك في قنوات).



2. اختيار خدمة → اختيار دولة

عرض قائمة الدول (اسم + علم + كود دولي).

أزرار: 🌍 تغيير الدولة، 🔙 رجوع.



3. طلب رقم

الرقم يأتي من مخزون الأرقام الذي أضافه الأدمن (لا يُجلب من API).

عند الحجز: تُسجَّل حالة الرقم = reserved للمستخدم والخدمة والدولة.

يُعرض:

الرقم (قابل للنسخ).

أزرار: 🔄 تغيير الرقم (مجانًا), 🌍 تغيير الدولة, 🔙 الرئيسية.




4. انتظار الكود (Monitor)

الـ API يُستخدم فقط لمراقبة الرسائل الواردة واستخراج كود التحقق (OTP).

البوت في “وضع التحقق” يراقب الـ API ويطابق الرسائل بالرقم المحجوز.

عند العثور على الكود:

يُرسل للمستخدم: الرقم + الكود (قابل للنسخ).

يُخصم السعر المحدَّد للخدمة من رصيد المستخدم.

تُحدَّث حالة الرقم = used ويُحذف من قائمة المتاح.


إذا لم يصل كود خلال مهلة (قابلة للتعيين): لا خصم، وخيارات: استبدال رقم أو إلغاء.





---

2) نظام الرصيد (Balance)

لكل مستخدم حقل رصيد.

الخصم يتم فقط عند وصول كود صالح.

إذا لم يصل كود ضمن المهلة: لا خصم.

سجل معاملات (transactions) لأي إضافة/خصم مع السبب والوقت.



---

3) تجميع رصيد مجاني عبر قنوات الاشتراك (Forced/Promo Channels)

3.1 تدفّق المستخدم

زر 🆓 تجميع رصيد مجاني.

يعرض قائمة القنوات (أزرار: انضمام + زر ✅ تحقق من الاشتراك).

عند الضغط على ✅ تحقق:

يتحقق البوت عبر getChatMember من اشتراك المستخدم في كل القنوات المطلوبة.

عند نجاح التحقق:

يضيف رصيدًا (قيمة يحددها الأدمن).

يُظهر رسالة نجاح مع الرصيد المضاف وباقي الرصيد.


عند الفشل:

يُظهر القنوات غير المكتملة الاشتراك فيها.




3.2 إدارة من الأدمن

إضافة/حذف قنوات.

تعيين:

قيمة الرصيد المجاني (مثلاً 5).

الدورية: مرة واحدة فقط / كل X ساعات / كل X أيام (يُحفظ آخر تاريخ مكافأة للمستخدم).


مكافحة التحايل:

حفظ user_id + channel_id + last_award_at.

لا تُمنح مكافأة متكررة قبل انقضاء الفترة المحددة.

إعادة التحقق قبل كل منحة.




---

4) لوحة الأدمن (داخل البوت)

4.1 إدارة الخدمات

➕ إضافة خدمة (اسم + إيموجي + وصف اختياري + سعر افتراضي).

➖ حذف خدمة.

⏸️ إيقاف / ✅ تفعيل خدمة.

📋 عرض الخدمات (اسم، حالة، سعر).

شرط العرض للمستخدم: الخدمة مفعّلة ومربوطة بموفّر API للتحقق (لضمان إمكانية استلام الأكواد).


4.2 إدارة الدول

➕ إضافة دولة (اسم + كود دولي مثل +20 + علم).

➖ حذف دولة.

📋 عرض الدول.


4.3 إدارة الأرقام (مخزون يدوي من الأدمن)

📂 رفع ملف أرقام (csv/txt).

✍️ إضافة رقم يدوي.

📋 عرض الأرقام المتاحة (مع ترشيح: خدمة/دولة/حالة).

🗑️ حذف رقم.

قواعد:

الرقم فريد لكل خدمة/دولة.

حالات الرقم: available | reserved | used | deleted.

عند الحجز يتم ختم reserved_by, reserved_at, ومدة المهلة.



4.4 إدارة موفّري الـ API (لأكواد التحقق فقط)

➕ إضافة موفّر API (الاسم، base_url، المفتاح، طريقة الجلب Poll/Webhook، تعبيرات/نمط استخراج الكود RegEx لكل خدمة).

🔄 ربط خدمة بموفّر (خدمة → مزوّد).

📋 عرض/تعطيل/حذف مزوّد.

إعدادات المراقبة:

فترة الـ Poll (ثوانٍ).

مهلة انتظار الكود بعد الحجز (دقائق).

RegEx على مستوى الخدمة (مثال واتساب: \b\d{6}\b).



4.5 إدارة المستخدمين

➕ إضافة رصيد (بالآيدي).

➖ خصم رصيد (بالآيدي).

📋 عرض رصيد مستخدم.

👥 عرض جميع المستخدمين مع صفحات (id, username, balance, joined_at, counters).

🔑 تعيين/إلغاء أدمن مساعد.


4.6 رسائل وإشعارات

📢 رسالة جماعية (لكل المستخدمين/مرشّح).

✉️ إرسال لمستخدم محدد.


4.7 إعدادات عامة

🛠 وضع الصيانة (تشغيل/إيقاف) – يُظهر للمستخدمين رسالة “البوت تحت الصيانة”.

📊 الإحصائيات:

إجمالي المستخدمين، الأرقام المحجوزة، الأكواد المستلمة، الأرباح، تفصيل حسب خدمة/دولة.


📡 قنوات الاشتراك الإجباري (إضافة/حذف/عرض).

🔑 تغيير باسورد الأدمن.


4.8 إدارة البوتات (اختياري عند تشغيل عدّة بوتات بنفس النواة)

➕ إضافة بوت (token + @username + باسورد إدارة محلي).

📋 عرض البوتات.

🗑️ حذف بوت.

كل بوت له قيود/إعدادات منفصلة (قنوات/أسعار/موفّري API/مخزون أرقام).



---

5) واجهة الأزرار (Inline Keyboards)

رئيسية:

الخدمات | 🆓 تجميع رصيد مجاني | 💰 رصيدي | ℹ️ مساعدة


اختيار خدمة → قائمة الدول (قائمة إنلاين مع pagination إذا لزم).

بعد حجز رقم:

🔄 تغيير الرقم | 🌍 تغيير الدولة | 🔙 الرئيسية


تجميع رصيد مجاني:

لكل قناة: زر انضمام + زر عام ✅ تحقق من الاشتراك.




---

6) حالات (States) وسير العمل (State Machine)

idle → select_service → select_country → reserve_number → waiting_code →

عند كود صحيح: code_received → خصم رصيد → completed.

عند انتهاء مهلة: expired → عرض خيارات (استبدال/إلغاء).




---

7) قواعد التسعير والخصم

السعر الافتراضي على مستوى الخدمة (قابل للتخصيص لكل دولة إن لزم).

الخصم = عند وصول الكود فقط.

سجل معاملة: (type=purchase, amount, service, number_id, user_id, timestamp).



---

8) استيراد الأرقام (ملف CSV/TXT)

تنسيق CSV مقترح (رأس الأعمدة):
service,country_code,phone_number,price_override(optional)
مثال:
WhatsApp,+20,201234567890,

TXT (سطر لكل رقم) مع اختيار الخدمة والدولة قبل الاستيراد.

فحص:

إزالة مكررات.

التحقق من الصيغة (^\+?\d+$).

تعيين الحالة = available.




---

9) تكامل موفّر الـ API (للكود فقط)

9.1 نمط Poll (سحب دوري)

إعداد فترة السحب (مثلاً كل 5 ثوانٍ).

endpoint يَرجع رسائل حديثة (JSON).

لكل رسالة:

استخراج الرقم المستهدف.

محاولة استخلاص الكود عبر RegEx حسب الخدمة.

العثور على حجز مطابق (number + service) في حالة waiting_code.

عند نجاح:

ربط الرسالة بالحجز.

إرسال الكود للمستخدم.

الخصم + إغلاق الحجز.




9.2 نمط Webhook (اختياري)

استقبال POST من المزود.

نفس منطق المطابقة والاستخراج والخصم.


9.3 أمثلة حقول رسائل مزوّد

{
  "messages": [
    {
      "to": "+201234567890",
      "service": "WhatsApp",
      "text": "Your verification code is 123456",
      "ts": "2025-08-28T14:25:00Z",
      "meta": { "provider_id": "X" }
    }
  ]
}

RegEx افتراضي واتساب/تليجرام: \b\d{5,6}\b (قابل للتخصيص).


9.4 مطابقة الرقم

المطابقة الصارمة بالصيغة الدولية (+CCXXXXXXXXXX).

دعم تنعيم (إزالة مسافات/شرطات) قبل المطابقة.


9.5 مهلة الحجز

عند reserve_number يحدد expires_at (مثلاً 15–30 دقيقة).

إن انتهت: إلغاء الحجز تلقائيًا، لا خصم.



---

10) الأمن والامتثال

حفظ سر الأدمن وكافة مفاتيح الـ API في متغيرات بيئية (ENV).

سجل تدقيق (audit) لكل عمليات الأدمن.

منع تسريب التوكن/المفاتيح في السجلات.

احترام شروط استخدام المنصّات التي يتم تلقي الأكواد لها (مسؤولية المالك).

معدّل طلبات (rate limit) لحماية من الإساءة.



---

11) الجداول (نموذج قاعدة بيانات مقترح)

users

id (pk), telegram_id (unique), username, first_name, last_name,
balance decimal(12,2) default 0, joined_at, is_admin bool, is_banned bool,
last_reward_at (nullable).


services

id, name unique, emoji, description, active bool, default_price decimal(12,2).


service_countries

id, service_id fk, country_name, country_code (e.g. +20), active bool.


numbers

id, service_id fk, country_code, phone_number unique,
status enum(available,reserved,used,deleted),
reserved_by_user_id (nullable), reserved_at (nullable), expires_at (nullable),
code_received_at (nullable), price_override (nullable).


providers (موفّرو API للأكواد)

id, name, base_url, api_key, mode enum(poll,webhook), poll_interval_sec, active bool.


service_provider_map

id, service_id fk, provider_id fk, regex_pattern (لكل خدمة).


provider_messages

id, provider_id fk, raw_payload jsonb, received_at.


reservations

id, user_id fk, service_id fk, number_id fk, status enum(waiting_code,completed,expired,canceled),
created_at, completed_at (nullable), expired_at (nullable), code_value (nullable).


transactions

id, user_id fk, type enum(add,deduct,purchase,reward), amount decimal(12,2), reason, created_at.


channels (قنوات الاشتراك/المكافآت)

id, title, username_or_link, required bool, active bool, reward_amount decimal(12,2) (اختياري).


user_channel_rewards

id, user_id fk, channel_id fk, last_award_at (nullable), times_awarded int.



---

12) أوامر وأزرار البوت

للمستخدم

/start

💰 رصيدي → إظهار الرصيد.

🆓 تجميع رصيد مجاني → قائمة القنوات + ✅ تحقق.

الخدمات → قائمة الخدمات.

داخل الخدمة: اختيار الدولة → احجز رقم.


للأدمن

/admin → لوحة التحكم (تتحقق بباسورد الأدمن عند أول دخول للجلسة).

إدارة الخدمات/الدول/الأرقام/المزوّدين/القنوات/المستخدمين/الرسائل/الإحصائيات/الصيانة.

كل قسم بـ Inline Keyboard مع صفحات.



---

13) منطق “وضع التحقق” (Pseudo)

loop every poll_interval_sec:
  msgs = provider.fetch_new_messages()
  for msg in msgs:
      number = normalize(msg.to)
      service = msg.service or infer_from_text(msg.text)
      code = extract(msg.text, regex_for(service))
      if not code: continue
      res = find_active_reservation(number, service, status='waiting_code')
      if not res: continue
      mark numbers[res.number_id].status = 'used'
      res.status = 'completed'
      res.code_value = code
      res.completed_at = now
      price = numbers.price_override or services.default_price
      deduct user.balance by price
      create transaction(user_id=res.user_id, type='purchase', amount=price, reason=f'{service} {number}')
      notify_user(res.user_id, f"رقمك: {number}\nالكود: {code}")

عند expires_at < now ولم يصل كود:

res.status = 'expired', رقم يرجع available أو يُستبدل حسب الإعداد.




---

14) الصيانة والأخطاء

رسالة موحّدة عند الصيانة.

رسائل أخطاء واضحة:

نفاد مخزون الأرقام لتلك الخدمة/الدولة.

عدم كفاية الرصيد عند محاولة الاستبدال المدفوع (إن وُجد).

لم يتم العثور على موفّر API مرتبط بالخدمة.

فشل التحقق من الاشتراكات.




---

15) الإعدادات (ENV)

BOT_TOKEN

ADMIN_PASSWORD

DATABASE_URL

POLL_INTERVAL_SEC (افتراضي 5)

RESERVATION_TIMEOUT_MIN (مثلاً 20)

DEFAULT_REWARD_AMOUNT (مثلاً 5)

مفاتيح موفّري API (لكل مزوّد): PROVIDER_X_API_KEY, PROVIDER_X_URL, ...



---

16) التقارير والإحصائيات

إجمالي المستخدمين.

أرقام محجوزة/مستخدمة/منتهية.

أكواد مستلمة حسب خدمة/دولة.

أرباح إجمالية وفترة زمنية.

أفضل الدول/الخدمات أداءً.

مكافآت القنوات الموزعة.



---

17) سياسات إضافية

كل العمليات المهمّة تُسجَّل (audit).

منع الأزرار القديمة (callback_data) بعد انتهاء صلاحيتها.

منع تكرار النقر عبر debounce/answerCallbackQuery.

تنظيف الرسائل الحسّاسة (حذف الكود بعد زمن اختياري في الخاص).

احترام سياسات تيليجرام ومزوّدي الخدمات المستهدفة.



---

18) نماذج رسائل واجهة

ترحيب:

أهلاً بك 👋
اختر خدمة للحصول على رقم مؤقت:

اختيار دولة:

اختر الدولة للخدمة: WhatsApp

رقم محجوز:

✅ تم حجز رقمك:
+20XXXXXXXXXX
سيتم إرسال كود التحقق هنا فور وصوله.

وصول كود:

🎉 وصل الكود!
الرقم: +20XXXXXXXXXX
الكود: 123456
تم خصم السعر من رصيدك.

تجميع رصيد:

اشترك في القنوات التالية ثم اضغط "✅ تحقق" للحصول على رصيد مجاني.



---

19) ترقيم الصفحات (Pagination)

عند عرض قوائم طويلة (المستخدمين/الأرقام/الخدمات/الدول):

أزرار: ⏮️ السابق | ⏭️ التالي | 🔙 رجوع.

حجم الصفحة افتراضي 10–20 عنصرًا.




---

20) صلاحيات الأدوار

owner (مالك): كل الصلاحيات + إدارة الأدمن المساعد.

admin (مساعد): إدارة مخزون/قنوات/مزودين/مستخدمين (بحسب تمكين المالك).

user: استخدام الخدمات وتجميع الرصيد.



---

بهذا تكون المواصفات واضحة:

الأرقام تُدار يدويًا من الأدمن (رفع/إضافة/حذف/حالات).

الـ API مخصّص لاستخراج الأكواد فقط عبر مراقبة الرسائل (Poll/Webhook) وربطها بالحجوزات.

الخصم عند وصول كود صالح فقط.

رصيد مجاني عبر الاشتراك في القنوات مع تحقّق ودورية ومكافحة تحايل.

كل شيء من داخل البوت عبر لوحة أدمن وأزرار إنلاين.


برومت — آلية العمل + إضافة الموارد + المنطق (قابل للتنفيذ)


---

ملخص سريع

الأرقام: يُضيفها الأدمن (يدويًا أو رفع ملف).

الـ API: يُستخدم فقط لاستخراج أكواد التحقق (OTP) عبر Poll أو Webhook.

الخصم من الرصيد: عند وصول كود صالح فقط بعد مطابقة الرقم المحجوز.

كل العمليات والإدارة تتم من داخل البوت (لوحة أدمن داخل التليجرام).



---

1) آلية حجز رقم ووقت حياته (Reservation Flow)

1. المستخدم يختار خدمة → يختار دولة → يطلب رقم.


2. النظام يختار number من جدول numbers حيث status = available وservice_id وcountry_code مطابقان.


3. يُنشأ سجل في reservations:

status = waiting_code, reserved_by = user_id, reserved_at = now, expires_at = now + RESERVATION_TIMEOUT.



4. يُحدث رقم الحالة إلى reserved.


5. يظهر للمستخدم واجهة بها: الرقم + أزرار (🔄 تغيير الرقم, 🌍 تغيير الدولة, 🔙 الرئيسية).


6. إذا ضغط المستخدم 🔄 تغيير الرقم:

إلغاء الحجز الحالي (reserved → available، مسح reserved_by).

حجز رقم جديد بنفس المنطق دون خصم.



7. إذا انقضت expires_at ولم يصل كود:

reservation.status = expired، number.status = available.

إشعار للمستخدم مع خيارات (احجز رقم جديد / اتصل بالدعم).





---

2) منطق مراقبة الأكواد (Monitor Mode)

Poll Mode (دوري): كل poll_interval_sec نفعل:


msgs = provider.fetch_new_messages()
for msg in msgs:
  to = normalize_number(msg.to)
  service = msg.service or infer_service(msg.text)
  code = regex_extract(msg.text, service_regex)
  if not code: continue
  res = find_reservation(number=to, service=service, status='waiting_code')
  if res:
    complete_reservation(res, code)

Webhook Mode: عند استقبال POST بنفس المنطق أعلاه.

دالة complete_reservation(res, code):

numbers[res.number_id].status = 'used'

res.status = 'completed', res.code_value = code, res.completed_at = now

السعر = numbers.price_override or services.default_price

لو user.balance >= price أو لو التخطيط أن الرصيد يُخصم بعد وصول الكود: deduct_balance(user_id, price)

سجل transactions مع type='purchase'

أرسل رسالة للمستخدم: رقم: {number}\nكود: {code}

تنظيف: حذف الرقم من قائمة المتاح نهائيًا أو وسمه used.




---

3) إضافة الموارد (من لوحة الأدمن داخل البوت)

3.1 إضافة خدمة

بيانات: name, emoji, description, default_price, active(bool)

إجراء: /admin → إدارة الخدمات → ➕ إضافة خدمة → يدخل الحقول.


3.2 إضافة دولة

بيانات: country_name, country_code (مثلاً +20), flag

إجراء: إدارة الدول → ➕ إضافة دولة.


3.3 إضافة أرقام (مخزون)

طريقتان:

رفع ملف CSV/TXT: تنسيق مقترح service,country_code,phone_number,price_override(optional)

إضافة يدوي: service + country_code + phone_number + price_override


عند الاستيراد:

تنظيف: normalize(phone_number), إزالة مسافات/شرطات، التأكد من الصيغة ^\+?\d+$

إزالة المكرر: تجاهل إذا كان موجودًا.

تعيين status = available.



3.4 إضافة موفّر API (للكود فقط)

بيانات: name, base_url, api_key, mode (poll|webhook), poll_interval_sec, service_regex_map

ربط مزوّد بخدمة: خريطة تحدد أي regex يستخدم لاستخراج الكود لكل خدمة.


3.5 إضافة قنوات تجميع رصيد

بيانات: channel_link, reward_amount, frequency (once/daily/weekly/...), active

حفظ آخر وقت منحة لكل مستخدم لكل قناة لمنع التكرار.



---

4) قواعد ومقيدات منطق المنظومة

مطابقة الرقم: تطابق صارم بعد normalize إلى الصيغة الدولية +CCXXXXXXXX.

استخراج الكود: لكل خدمة RegEx منفصل (مثال: \b\d{5,6}\b). قابل للتعديل لكل مزوّد.

مهلة الحجز: RESERVATION_TIMEOUT (مثلاً 15–30 دقيقة). انتهاء المهلة يعيد الرقم إلى available.

الخصم: يُنفَّذ فور إتمام الحجز واستلام الكود (Atomic transaction: تحديث رصيد + تحديث حالات).

منع التحايل في المكافآت: تحقق getChatMember عند طلب ✅ تحقق، وسجل last_award_at لمنع تكرار الدفعة قبل انقضاء frequency.

حالات الخطأ:

لا يوجد أرقام متاحة → رسالة "لا يوجد أرقام لهذه الدولة/الخدمة".

لا يوجد موفّر API مرتبط → رسالة "الخدمة غير مفعلة لعدم ربط API".

فشل الخصم (رصيد غير كاف) — افتراضي: إبلاغ المستخدم وترك reservation منتهية أو إلغاء الحجز.




---

5) بيانات مهمة للجداول (مقتطف)

users(telegram_id, username, balance, joined_at, is_admin, last_reward_at)

services(id, name, emoji, default_price, active)

service_countries(service_id, country_name, country_code)

numbers(id, service_id, country_code, phone_number, status, reserved_by, reserved_at, expires_at, price_override)

providers(id, name, base_url, api_key, mode, poll_interval_sec, active)

service_provider_map(service_id, provider_id, regex_pattern)

reservations(id, user_id, service_id, number_id, status, created_at, completed_at, code_value)

transactions(id, user_id, type, amount, reason, created_at)

channels(id, link, reward_amount, frequency, active)

user_channel_reward(id, user_id, channel_id, last_award_at, times_awarded)



---

6) واجهات الأزرار (اقتراح callback_data)

svc_{service_id} — اختيار خدمة

cty_{country_code} — اختيار دولة

resv_change_{reservation_id} — تغيير رقم

resv_cancel_{reservation_id} — إلغاء/انتهاء

admin_add_num / admin_import_csv / admin_add_provider / admin_channels_add إلخ.



---

7) سيناريوهات نمطية (مثالان)

سيناريو A — نجاح كامل

1. المستخدم يحجز رقم → reservation.waiting_code.


2. المزوّد يرسل رسالة للكود إلى +201234....


3. Poll يلتقط الرسالة → يستخرج 123456 → يجد الحجز → يكمل الحجز → يخصم 10 جنيه → يبلّغ المستخدم.



سيناريو B — انتهاء مهلة

1. المستخدم يحجز رقم، لا يصل كود خلال 20 دقيقة → الحجز expired → العدد يعود available → المستخدم يُخطر بخيار استبدال أو إلغاء → لا خصم.




---

8) متطلبات التنفيذ التفصيلية قصيرة

احتفظ بالسجلات (audit) لكل تغيير يفعله الأدمن.

عمليات حسّاسة (خصم/إكمال) تتم داخل معاملة قاعدة بيانات (transaction).

المتغيرات القابلة للتعديل من لوحة الأدمن: RESERVATION_TIMEOUT, POLL_INTERVAL_SEC, DEFAULT_REWARD_AMOUNT, PAGE_SIZE.

واجهة /admin تتطلب تحقق بباسورد الجلسة.

قيود أمان: مفاتيح في ENV، لا تطبع الـ API keys في اللوجز.



---

9) Pseudo لحالة إتمام الحجز (atomic)

begin transaction
  if reservation.status != 'waiting_code': rollback & return
  if user.balance < price: mark reservation.failed & notify user & rollback
  update numbers.status = 'used'
  update reservation.status = 'completed'; set code_value, completed_at
  insert transaction(user_id, 'purchase', price, reason)
  update users.balance -= price
commit
notify user with number + code


---

النص أعلاه جاهز لكي يحوّله AI Agent إلى مهام تنفيذية (endpoints، جداول DB، دوال Poll/Webhook، واجهات Inline keyboards، واجهة /admin).

