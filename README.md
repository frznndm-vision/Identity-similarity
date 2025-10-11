# Frontalization-Face
<div dir="rtl">
این تسک با محوریت **فرانتال‌سازی چهره-(چرخش به نمای رو یه رو)** انجام شده است تا ارزیابی‌ها و خروجی‌ها به‌صورت شفاف و قابل بازبینی کنار هم قرار بگیرند.

## خلاصهٔ اجرایی 
- **هدف اصلی:** تبدیل تصاویر زاویه‌دار به نمای روبه‌رو (Frontal) به منظور بهبود میزان شباهت به (Ground Truth)**GT**.
- **سؤال کلیدی:** آیا <code dir="ltr">Frontal ↔ GT</code> از <code dir="ltr">Angle ↔ GT</code> **بهتر** است؟ یکی از راه‌های متداول ارزیابی مدل‌های فرانتال‌سازی، سنجش شباهت به GT است تا اطمینان حاصل شود **هویت فرد پس از فرانتال‌سازی حفظ می‌شود**.
- **داده‌های معیار:** BIWI (۲۴ هویت) +  LFW برای ارزیابی مکمل.
-  **مدل‌ها:** LivePortrait (نسخهٔ ادیت‌شده)، FacePoke، FSRT، Face-frontalization-in-image-sequences-using-GAN-Inversion، AniPortrait، scaleway/frontalization، FFWM.  
  <span style="font-size:12px">(فهرست کامل مدل‌های بررسی‌شده در جدول «Related Models» انتهای این گزارش آورده شده است.)</span>
---




### داده‌ها
<div dir="rtl">

- **BIWI** — مجموعه‌ای از تصاویر/ویدئوهای چهره با زوایای متنوع سر، شامل **۲۴ هویت**.  
  مناسب سنجش شباهت در شرایط **زاویه‌دار** و ارزیابی اثر فرانتال‌سازی.

* **LFW** — دیتاست کلاسیک تشخیص هویت؛ برای هر فرد معمولاً چند تصویر دارد و ما آن‌ها را **پوشه‌به‌ازای‌هویت** مرتب کردیم تا جفت‌های **مثبت/منفی** را کنترل‌شده بسازیم و شباهت هویتی عمومی را بسنجیم.


</div>

### نکتهٔ اجرایی
در جریان آزمایش‌ها مشاهده شد برخی مدل‌ها عملکرد مطلوبی نداشتند؛ بنابراین **مدل LivePortrait** شخصی‌سازی/ادیت شد تا **هر تصویر ورودی** را به نمای روبه‌رو تبدیل کند.
<div dir="rtl">

</div>


## نتایج کمی مدل LivePortrait
### 1) شباهت فرانتال در برابر زاویه‌دار نسبت به GT

<div align="center">

| Model | Dataset | Mean(Angle↔GT) | Mean(Frontal↔GT) | Net Gain | N |
|---|---|---:|---:|---:|---:|
| LivePortrait | BIWI | 0.82 | 0.52 | -0.30 | 15678 |
| LivePortrait  | LFW | 0.66 | 0.61 | -0.4 | 13234 |
---
</div>

**فرمول:** `Net Gain = similarity(Frontal, GT) − similarity(Angle, GT)`


### 2)  نتایج بهبود مدل بر شباهت (پس پردازش و پیش پردازش) 

درجهت حفظ هویت افراد در فرانتال سازی و بهبود افزایش شباهت بین عکس فرانتال و GT بهبود هایی هم انجام شد که به شرح زیر می باشد.


### پس‌پردازش (Post-processing)
- تصحیح‌های سبک (مثلاً حذف حاشیه‌های ناخواسته، یکنواخت‌سازی رنگ/شارپنس در حد کم).
- اطمینان از سازگاری اندازه و کادر خروجی با ورودی‌های مرجع برای مقایسهٔ دقیق.
- تلاش برای حفظ embedding های خروجی توسط insightface
- بهبود کیفیت تصاویر خروجی از  LivePortrait توسط مدل GPFGAN
- حذف بک گراند تصاویر


<div align="center">
  
| Enhancement | Retina+R100 | SCRFD+R100 | Retina+R50 | SCRFD+R50 | N |
|---|---|---:|---:|---:|---|
| Base | - | 0.519 | - | - | 484 |
| GPFGAN | 0.565 | 0.554 | 0.528 | 0.521 | 484 |
| Sharp | 0.556 | 0.547 | 0.525 | 0.516 | 484 |
| Omit-bg | 0.82 (804 Face Not Detect) | 0.499 | 0.814 (804 Face Not Detect) | 0.468 | 484 |
---
</div>

 ###تطابق هویتی

«خروجیِ فرانتال‌شده با مرجعِ همان فرد (GT) شباهت بیشتری دارد تا با افراد دیگر؛ بنابراین شاید بتوان نتیجه گرفت هویت حفظ می‌شود.»
تصویر سمت چپ تصویر اصلی میباشد.

<p align="center">
  <img src="ordered_similarity_uniform_spaced.png" alt="FFWM failure">
</p>
«نمونهٔ خودش و نسخهٔ زاویه‌دار، ۰٫۷۴ شباهت دارند (≈۷۴٪).»

## نتایج کیفی مدل Live Portrait


### A) Angled → Frontal → GT (سه‌تایی نمونه)
<p align="center">
  <img src="AngleVsGtVsFrontal_triple.png" alt="FFWM failure">
</p>


### B) Model Output Quality — LFW & BIWI

<p align="center">
  <img src="lfw_biwi_compact.png" alt="FFWM failure">
</p>

---


## یادداشت‌ها
<div align="center" dir="rtl">

## 🔎 Related Models (Full List)

| Model | Code | Paper | Repo | Trainable | Short Note |
|:--:|:--:|:--:|:--:|:--:|--|
| FacePoke | ✓ | ✓ | [GitHub](https://github.com/jbilcke-hf/FacePoke) | ✓ | ابزار تعاملی مبتنی بر LivePortrait برای جابه‌جایی سر و حالت‌های چهره |
| LivePortrait | ✓ | ✓ | [GitHub](https://github.com/KwaiVGI/LivePortrait) | ✓ |  مدل سبک و سریع برای انیمیشن پرتره با کلیدپوینت‌های ضمنی، پشتیبانی از **retargeting** و **stitching**؛ مناسب تولید حرکت روان از یک تصویر ثابت و پایهٔ خوبی برای فرانتال‌سازی.|
| headGAN | ✓ | ✓ | [GitHub](https://github.com/michaildoukas/headGAN) | ✓ | بازاجرای چهره و ساخت آواتار ویدیویی از یک تصویر ثابت—تغییر حالت/زاویه، دیپ‌فیک کنترل‌شده و افزایش دادهٔ آموزشی. |
| FSRT | ✓ | ✓ | [GitHub](https://github.com/andrerochow/fsrt) | ✓ |  مدل ترنسفورمری برای بازاجرای چهره که با تفکیک **ظاهر** از **هدپوز/احساسات**، حرکت را از ویدئوی محرّک به چهرهٔ منبع منتقل می‌کند (CVPR 2024). |
| Fantasy-Portrait | ✓ | ✓ | [GitHub](https://github.com/Fantasy-AMAP/fantasy-portrait) | – | برای انیمیشن/reenactment طراحی شده؛ می‌شه با درایور خنثی به نمای نزدیک فرانتال رسید، اما در سناریوی فرانتال‌سازی خالص پایداری و حفظ هویت ضعیف‌تر از LivePortraitه. |
| HunyuanPortrait | ✓ | ✓ | [GitHub](https://github.com/Tencent-Hunyuan/HunyuanPortrait) | – |  مدل دیفیوشنی تنسنت برای انیمیشن/بازاجرای پرتره تک‌تصویری با کنترل **pose/expression** و تأکید بر پیوستگی زمانی و کیفیت بصری.|
| X-Portrait | ✓ | ✓ | [GitHub](https://github.com/bytedance/X-Portrait) | – | مدل بایت‌دنس برای بازاجرای/انیمیشن پرتره از یک تصویر ثابت با درایور ویدئویی |
| First-Order-Model | ✓ | ✓ | [GitHub](https://github.com/AliaksandrSiarohin/first-order-model) | ✓ | مدل کلیدیِ انیمیشن تصویر با کی‌پوینت‌های یادگرفتنی و میدان حرکت چگال که یک عکس ثابت را با ویدئوی محرّک به‌صورت واقع‌گرایانه به حرکت درمی‌آورد |
| scaleway/frontalization | ✓ | – | [GitHub](https://github.com/scaleway/frontalization) | – |  پیاده‌سازی سادهٔ فرانتال‌سازی چهره با GAN و DALI—مناسب آزمایش/آموزش، نه کیفیت تولیدی در حد مدل‌های مدرن.|
| FFWM | ✓ | ✓ | [GitHub](https://github.com/csyxwei/FFWM) | ✓ | روش فرانتال‌سازی مبتنی بر وارپ/جریان نوری (optical flow) با ماژول‌های ویژهٔ هم‌ترازی—در نورهای نامتوازن حساس است و ممکن است آرتیفکت بدهد. |
| DA-GAN | – | – | [GitHub](https://github.com/YuYin1/DA-GAN) | – | فرانتال‌سازی زاویه‌های بزرگ با مکانیزم **Dual-Attention** (self + face) برای حفظ هویت و جزئیات در نماهای سخت. |
| TP-GAN | ✓ | ✓ | [GitHub](https://github.com/HRLTY/TP-GAN) | – | فرانتال‌سازیِ چهره با معماری **دو‌مسیره** (مسیر سراسری + مسیرهای محلی برای چشم/بینی/دهان) جهت حفظ هویت و جزئیات در زاویه‌های بزرگ |
| DECA | ✓ | ✓ | [GitHub](https://github.com/yfeng95/DECA) | ✓ | بازسازی سه‌بعدی چهره با **3DMM** از یک تصویر و رندر مجدد؛ امکان **صفر کردن pose** و گرفتن نمای فرانتال با حفظ ساختار هندسی. |
| NVLabs EG3D | ✓ | ✓ | [GitHub](https://github.com/NVlabs/eg3d?utm_source=chatgpt.com) | ✓ | با **inversion** می‌توان از یک چهره نماهای جدید ازجمله **frontal** رندر کرد، |
| HRN | ✓ | ✓ | [GitHub](https://github.com/youngLBW/HRN) | ✓ | بازسازی سه‌بعدی چهره از یک تصویر |
| pixel3DMM | ✓ | ✓ | [GitHub](https://github.com/SimonGiebenhain/pixel3dmm) | – |  بازسازی سه‌بعدی چهره با و رندرساز سازگار با نما—برای چرخاندن pose و گرفتن نمای **frontal** مفید است، ||
| CVTHead | ✓ | ✓ | [GitHub](https://github.com/HowieMa/CVTHead) | ✓ | روش WACV 2024 برای ساخت آواتارِ سرِ قابل‌کنترل از یک تصویر واحد با *Vertex-Feature Transformer*؛ با ضرایب **3DMM** امکان کنترل صریح **pose/expression/view** را می‌دهد. |
| Face-frontalization-in-image-sequences-using-GAN-Inversion | ✓ | – | [GitHub](https://github.com/mhahmadi258/Face-frontalization-in-image-sequences-using-GAN-Inversion) | ✓ | **Face-frontalization-in-image-sequences-using-GAN-Inversion:** فرانتال‌سازی چهره در توالی تصاویر با **GAN Inversion**؛ از یک ویدئو/دنبالهٔ تک‌فریم‌ها، نمای روبه‌رو تولید می‌کند با تأکید بر **حفظ هویت** و **سازگاری زمانی** (پارامتر کم و اجرای سبک).|

> **یادداشت:** ستون‌های «Code/Paper» بر اساس لینک‌های موجود شما پر شده‌اند. اگر برای هر کدام مقاله/لینک دقیق‌تری دارید، اضافه می‌کنم. همچنین لینک‌های تکراری/خراب (مثل DECA دوبار) یک‌دست شدند.

</div>



- من این مدل‌ها را هم برای تسک فرانتال سازی اجرا گرفتم، اما هر کدام به دلیلی نتیجهٔ مطلوبی نداشتند و در بسیاری موارد خروجی‌ها واقعاً ضعیف (حتی «افتضاح») بود.


## Failure Cases — FFWM
در برخی نمونه‌ها، خروجی **FFWM** دچار آرتیفکت شدید و افت هویت شد. نمونه‌ی زیر:

<p align="center">
  <img src="ffwm_failure_title_ffwm.png" alt="FFWM failure">
</p>

## Failure Cases — DECA
در این ورودی، بازسازی سه‌بعدی و رندر مجدد باعث blur و artifact شده و شباهت ادراکی به GT کاهش یافته است

<p align="center">
  <img src="deca_samples.png" alt="DECA failure">
</p>
 </div>
<div dir="rtl">

همان‌طور که در نتایج دیده می‌شود، در بیشتر موارد **نمونه‌های زاویه‌دار (Angle)** شباهت بالاتری نسبت به **خروجی‌های فرانتال‌شده (Frontal)** در مقایسه با **GT** دارند. دو دلیل اصلی:  
1) **InsightFace** (برای ارزیابی شباهت) تا حدی نسبت به **زاویهٔ صورت** مقاوم/آگاه است و در برخی زوایا همچنان امتیاز شباهت خوبی می‌دهد.  
2) روش‌های **تولیدی (Generative)** مورد استفاده برای فرانتال‌سازی ممکن است بخشی از **جزئیات هویتی** را نرم/حذف کنند؛ بنابراین هرچند خروجی بصری قابل‌قبول است، **امتیاز شباهت** کاهش می‌یابد.  

بر همین اساس، تصمیم گرفتیم بررسی کنیم **کدام ترکیب InsightFace**  در برابر **تغییر زاویه (pose)** **مقاوم‌تر** است و پایدارترین برآورد شباهت را ارائه می‌دهد.

</div>

# Identity-similarity
## Face Identity Similarity (شباهت هویت چهره)

**مقایسهٔ شباهت چهره** برای سنجش اینکه کدام روش بیشترین شباهت هویتی را نسبت به تصویر مرجع (GT) حفظ می‌کنند و انتخاب بهترین مدل شباهت سنجی—در سه حالت:
- **بدون زاویه (Non‑Angled):** تصاویر تمام‌رخ یا با زاویه‌ی ناچیز.
- **زاویه‌دار (Angled):** مقایسه‌ی *Angle ↔ GT* و نیز *Angle ↔ Angle* (سازگاری درون‌زاویه‌ای).

### ترکیب‌های مدل برای محاسبهٔ شباهت
برای تشخیص چهره از **RetinaFace** و **SCRFD** و برای شناسایی/استخراج بردار هویت از **ResNet50** و **ResNet100** استفاده شده است. چهار ترکیب زیر ارزیابی شده‌اند:
- RetinaFace + ResNet100
- RetinaFace + ResNet50
- SCRFD + ResNet100
- SCRFD + ResNet50


---
### مدل‌های شباهت‌سنجی
- تشخیص چهره: **RetinaFace**، **SCRFD**
- استخراج ویژگی/شناسایی: **ResNet50**، **ResNet100**

---

### ترکیب‌های مورد ارزیابی برای شباهت
- RetinaFace + ResNet100  
- RetinaFace + ResNet50  
- SCRFD + ResNet100  
- SCRFD + ResNet50


## نتایج کمی مقایسه بهترین مدل شباهت سنجی
### 3) نتایج مدل شباهت سنجی
<div align="center">

| Group | Retina+R100 | SCRFD+R100 | Retina+R50 | SCRFD+R50 | N |
|---|---|---:|---|---|---|
| Angled vs GT | 0.723 | 0.740 | 0.705 | 0.726 | 15678 |
| Angled vs Angled | 0.714 | 0.725 | 0.695 | 0.707 | 15678 |
| GT vs GT | 0.974 | 0.971 | 0.977 | 0.975 | 15678 |
---
</div>
---
## نصب و اجرای کد (Setup & Inference)
<div dir="rtl">

### پیش‌نیازها
- GPU انویدیا + CUDA (پیشنهادی)
- Python 3.9 یا 3.10
- ffmpeg

### 1) ایجاد محیط
</div>

```bash
# با conda (پیشنهادی)
conda create -n frontal python=3.10 -y
conda activate frontal

# یا با venv
# python3 -m venv .venv && source .venv/bin/activate

<div dir="rtl">
2) نصب PyTorch (با CUDA شما)
</div>

) نصب وابستگی‌های پروژه
</div>
) اجرای (Frontalization)

نمونهٔ:

</div>

## اجرای نمونه (Quickstart)
```bash

# 2) فرانتال‌سازی 
python inference.py   --source_dir .../input   --source_pattern "*.png"   -o out   --frontalize 

