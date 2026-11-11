# Ucapin — Privacy Policy

**App:** Ucapin: Kartu Ucapan & Status (`com.talero.ucapin`)
**Developer:** Talero
**Contact:** talero.dev@gmail.com
**Effective date:** November 10, 2026
**Version:** 1.0, for app version 1.0.0 (versionCode 10000)

> This is the English text; the Indonesian text is published alongside it at https://github.com/talero-dev/ucapin-privacy-policy.
> Indonesian is the app's primary language; where the two texts differ, the Indonesian one
> governs for users in Indonesia.

## 1. The short version

Ucapin makes greeting cards and status images. It has no accounts and no sign-up. Everything you
make — your cards, the words you type on them, the photos you put behind them — stays on your
phone.

At most five things leave your phone, and only these five:

1. **What you fill into the AI writer**, when you press "Tulis dengan AI", is sent to Google to
   generate three suggestions.
2. **An AI suggestion you report** with the flag icon, together with the reason and your optional
   comment, is stored so we can review it.
3. **Usage and crash information** collected by Google's Firebase SDKs.
4. **Advertising requests**, handled by Google AdMob, after you have answered the consent form.
5. **Routine service traffic**: the settings fetch described in section 4.5, the push registration
   in section 4.6, and Google Play's review and update prompts in section 4.8. None of it carries
   anything you made.

We do not sell your data. We do not ask for your contacts, your location, your microphone or your
camera. We do not have a server of our own: the only third party involved is Google, in the roles
described in section 6.

## 2. Who we are

Ucapin is published by **Talero**, who is the data controller for the processing
described here. Write to **talero.dev@gmail.com** for any question about this policy, to ask what is
held about you, or to ask for something to be deleted.

## 3. What stays on your device

None of this is sent anywhere. It lives in the app's own private storage, which no other app can
read.

| What | Where it is kept | What it contains |
|---|---|---|
| Unfinished cards ("draf") | `ucapin.db`, table `drafts` | the template, the occasion, and the card itself as JSON — including every word you typed |
| Favourites | `ucapin.db`, table `favourites` | which templates, messages and occasions you starred |
| Export history | `ucapin.db`, table `exports` | the format, the template, the file path and the time of each card you saved or shared |
| Templates unlocked by watching a rewarded ad | `ucapin.db`, table `unlocked_templates` | the template id and when it was unlocked |
| Photos you put behind a card | `filesDir/photos/<uuid>.jpg` | a copy of the picture you chose, downscaled to 2160 px on its longest side |
| Settings | DataStore `ucapin_settings` | theme, dynamic colour, haptics, reminders on/off, your chosen occasions, whether onboarding is done |
| AI allowance | DataStore `ucapin_ai_quota` | how many free generations you have used today, and any credit earned from a rewarded ad |
| Ad timing | DataStore `ucapin_ad_counters` | when a full-screen ad was last shown, when a rewarded ad was last shown |
| Review timing | DataStore `ucapin_review` | when the review prompt was last shown |

Three notes on this.

**The Photo Picker.** Choosing a photo uses Android's system Photo Picker, which is why the app
asks for no permission to read your pictures — not `READ_MEDIA_IMAGES`, not anything. The picked image is copied into the app's own folder so
your card does not break when the temporary read access expires; the copy is deleted when you
delete the draft that uses it, when you use "Hapus data saya", or by a sweep at the next start
that removes any photo no saved draft points at.

**The clipboard.** "Salin teks" puts the message on the Android clipboard, which is a system
feature; from there it is out of the app's hands and subject to Android's own clipboard
behaviour.

**Backup.** If you have Android's Auto Backup switched on, Android copies `ucapin.db` and the
app's DataStore files — your settings, your AI allowance and the review timestamp — into your own
Google account, encrypted, under Google's backup terms. Three things are kept out of that backup:
your imported photos, the ad timing and consent state, and Firebase's own identifiers. This is
Android doing it, not the app; you can turn it off in your device settings.

## 4. What is sent, and when

Sections 4.1 to 4.6 describe Google Firebase services. They apply only where those services are
enabled in the app; where they are not, nothing described in those sections is sent, and the AI
writer uses the app's built-in message library instead.

### 4.1 The AI writer

Pressing "Buat" on the AI writer sends a request to **Firebase AI Logic**, which calls Google's
**Gemini Developer API**. The request contains exactly these fields, and nothing else:

- the occasion (its id and its English name) and whether the occasion is a somber one;
- the relationship you chose (friend, parent, colleague, and so on);
- the recipient's name, if you typed one — at most 30 characters;
- your own name, if you typed one — at most 30 characters;
- the tone and the length you chose;
- the language you asked the message to be written in;
- your extra note, if you wrote one — at most 120 characters.

Those values are sent inside a JSON data block, with instructions telling the model to treat them
as data. Before they are sent, the app strips control characters, code fences and
instruction-shaped phrases from the three free-text fields.

**Words you type in the card editor are never sent to the AI writer or anywhere else.** Neither
is the photo behind your card, nor the finished image.

Each request carries a Firebase **App Check** token, produced by Google Play Integrity, which
attests that the request came from a genuine installation of this app. It carries no information
about you.

Google processes this request as our service provider under the Firebase Data Processing and
Security Terms and the Gemini API additional terms. Ucapin does not keep a copy of the request or
of the generated text; the three suggestions are held in memory on your phone until you leave the
screen.

### 4.2 Reporting an AI suggestion

Every suggestion has a flag icon. Reporting one stores a single record in Google **Cloud
Firestore**, in the collection `ai_reports`, containing:

| Field | What it holds |
|---|---|
| `createdAt` | the server's timestamp |
| `reason` | one of `tidak_pantas`, `menyinggung_sara`, `konten_seksual`, `informasi_keliru`, `lainnya` |
| `comment` | your optional comment, at most 200 characters — omitted if you leave it blank |
| `occasion`, `tone`, `language` | the shape of the request that produced the text |
| `modelName`, `appVersion` | which model and which build of the app produced it |
| `text` | the reported suggestion itself, at most 4000 characters |
| `textHash` | a SHA-256 of that text, so duplicates can be grouped |

**The record does not contain the recipient's name, your name or your extra note**, and it does
not contain anything else from the app.

To write it, the app signs in to **Firebase Authentication** anonymously — an account with no
email, no password and no profile, created the first time you file a report and never before.
The identifier of that anonymous account is not written into the report.

The security rules on the collection allow one thing: creating a report. No client, including
this app, can read, edit or delete anything in it.

**Retention: 180 days.** Reports are reviewed and then deleted.

### 4.3 Analytics

The app sends usage events to **Google Analytics for Firebase**. The set of events is fixed in the
app's source code and cannot be extended at runtime. It is:

`screen_view`, `onboarding_complete`, `settings_change`, `consent_status`, `ad_request`,
`ad_loaded`, `ad_failed`, `ad_shown`, `ad_reward_earned`, `notification_opt_in`,
`review_prompt_shown`, `update_prompt_shown`, `occasion_view`, `template_open`, `editor_action`
(sampled), `ai_generate`, `ai_variant_used`, `ai_report`, `card_export`, `share`, `message_copy`,
`reminder_opt_in`.

Their parameters are occasion ids, template ids, format names, enum values, latency buckets and
counts. **No event carries text you typed, a name, a photo, or the content of a card.**
`editor_action`, for example, records that an edit was of type "text", "sticker" or "photo" — not
what the edit was.

Four user properties are set: `ui_theme`, `font_scale_bucket`, `app_language`, `consent_state`.

Analytics is keyed on a Firebase app-instance identifier, not on you. Google's own collection
also includes device and app information; see Google's documentation linked in section 6.

Analytics storage, ad storage, ad user data and ad personalisation are all set from your answer to
the consent form (section 5). If you refuse, all four are set to "denied".

### 4.4 Crash reporting

**Release builds only.** Debug and internal QA builds send nothing. **Firebase Crashlytics**
receives a stack trace, the device model, the Android version, the Firebase installation
identifier, and three custom keys: the screen you were on (`route`), the app language
(`app_language`), and a shape-only summary of app state (`feature_state`, for example "3 drafts,
AI on, reminders off"). No card content and no text you typed is attached to a crash report.

### 4.5 Remote configuration

**Firebase Remote Config** fetches the app's feature flags and limits — whether ads are on, the
AI model name and quota, the featured templates, the holiday calendar. The fetch identifies the
installation to Firebase; it sends nothing about what you have made.

### 4.6 Notifications

Reminders for occasions you chose are scheduled **on your device** by Android's WorkManager. No
server is involved and no occasion list is uploaded.

Separately, **Firebase Cloud Messaging** is used for seasonal announcements. The app subscribes
to two topics: `all`, and `lang_` followed by your display language (`lang_id`, `lang_en`). A
topic subscription tells Firebase the app's registration token and the topic name, nothing else.
A message goes to everyone on the topic; it is not addressed to you.

Notifications need the `POST_NOTIFICATIONS` permission on Android 13 and above. The app asks for
it when you turn reminders on, never at first launch, and works normally if you refuse.

### 4.7 Advertising

The app shows ads through the **Google Mobile Ads SDK** (AdMob). An ad request goes to Google and
may involve the Android advertising identifier or its Privacy Sandbox replacement, your
approximate location derived from your IP address, and information about the device and the app.
Google uses this to select and measure ads. See Google's advertising documentation in section 6.

Before any ad is requested, Google's **User Messaging Platform** (UMP) decides — from where you
are — whether a consent form is required, and shows it. No ad is requested until UMP reports that
ads may be requested. Where a form applies, you can reopen it at any time:
**Pengaturan → Opsi privasi iklan**.

Ads are not shown during onboarding, in Settings, on privacy or legal screens, or while you are
typing in the editor.

### 4.8 Google Play

**In-app review** and **in-app updates** use Google Play's own libraries. When the app asks Play
to show a review prompt or an update prompt, Play handles the interaction; the app is told only
that the flow finished, never what you wrote in a review.

## 5. Consent, and your choices

| You want to | Where |
|---|---|
| Change your advertising consent | Pengaturan → Opsi privasi iklan (shown when a form applies to you) |
| Stop reminders | Pengaturan → Pengingat, or the Android notification settings for Ucapin |
| Reset the Android advertising identifier | Android Settings → Privacy → Ads |
| Delete everything | Pengaturan → Hapus data saya (section 8) |
| Ask a question or make a request | talero.dev@gmail.com |

Refusing consent does not disable the app. The AI writer, the templates, the editor and sharing
all work the same; only ad personalisation and the analytics signals change.

## 6. Third-party SDKs, and what each one sees

Every SDK in the shipped app that can send anything off the device is listed here, with its
version. Google is the only third party.

| SDK | Version | What it sees |
|---|---|---|
| Google Mobile Ads (Next-Gen) | 1.5.0 | ad requests; advertising identifier or Privacy Sandbox equivalent; approximate location from IP; device and app information; ad interactions |
| User Messaging Platform (UMP) | 4.0.0 | your location for the purpose of deciding which consent form applies, and your answer to it |
| Google Analytics for Firebase | Firebase BoM 34.19.0 | the fixed event list in section 4.3; four user properties; app-instance id; device and app information |
| Firebase Crashlytics | Firebase BoM 34.19.0 | release-build crashes: stack trace, device model, OS version, installation id, three custom keys |
| Firebase Remote Config | Firebase BoM 34.19.0 | the fetch itself and the installation id |
| Firebase Cloud Messaging | Firebase BoM 34.19.0 | the registration token and the two topics `all` and `lang_<code>` |
| Firebase Installations | Firebase BoM 34.19.0 | the installation id the services above are keyed on |
| Firebase AI Logic (Gemini Developer API) | Firebase BoM 34.19.0 | the AI writer's form fields listed in section 4.1 |
| Firebase App Check + Play Integrity | Firebase BoM 34.19.0 | a device-integrity attestation for each AI request; no information about you |
| Cloud Firestore | Firebase BoM 34.19.0 | one report document per reported suggestion, as listed in section 4.2 |
| Firebase Authentication (anonymous) | Firebase BoM 34.19.0 | an account with no personal detail, created only when you file your first report |
| Google Play in-app review | 2.0.2 | that a review flow was requested |
| Google Play in-app update | 2.1.0 | that an update flow was requested |

Google's own explanations:

- Google Privacy Policy — https://policies.google.com/privacy
- How Google uses information from sites or apps that use our services —
  https://policies.google.com/technologies/partner-sites
- Privacy and security in Firebase — https://firebase.google.com/support/privacy
- Firebase Data Processing and Security Terms —
  https://firebase.google.com/terms/data-processing-terms
- AdMob and Android privacy settings — https://developers.google.com/admob/android/privacy
- Firebase AI Logic — https://firebase.google.com/docs/ai-logic
- Gemini API additional terms of service — https://ai.google.dev/gemini-api/terms
- Generative AI prohibited use policy —
  https://policies.google.com/terms/generative-ai/use-policy
- Firebase App Check — https://firebase.google.com/docs/app-check
- Play Integrity API — https://developer.android.com/google/play/integrity

## 7. Retention

| Data | Kept for |
|---|---|
| Everything in section 3 | until you delete it, or until you uninstall the app |
| Exported images in the app's cache | cleared automatically; the cache is short-lived and Android may clear it at any time |
| Images you saved to your gallery | yours — the app does not manage them after they are saved |
| A reported AI suggestion (section 4.2) | **180 days**, then deleted |
| The AI writer's request and response | not retained by us; Google's retention is governed by the terms linked in section 6 |
| Analytics events | Google's Analytics retention setting for the project, set by the developer in the Firebase console |
| Crash reports | Crashlytics' own retention |

## 8. Deleting your data

**Pengaturan → Hapus data saya**, then confirm. It deletes, in one action:

- every draft, favourite, export-history row and rewarded unlock in `ucapin.db`;
- every photo you imported;
- today's AI allowance and any rewarded credit;
- the review-prompt timestamp;
- your settings: theme, dynamic colour, haptics, reminders and your chosen occasions;
- with Firebase connected: it resets Google Analytics data, which drops queued events and mints a
  new app-instance identifier, and it deletes the Firebase installation identifier — after which
  Remote Config and Cloud Messaging see the device as a new one;
- the advertising consent state, so the consent form is presented again.

Onboarding is deliberately not shown again. **The app's language is not reset**: it is an Android
per-app setting rather than one of ours, and it is changed in Pengaturan → Bahasa or in the Android
settings for Ucapin.

Two things this cannot reach, stated plainly:

- **Reports you already filed.** The security rules on `ai_reports` deny deletion to every
  client, which is what makes a report evidence rather than something that can be edited
  afterwards. They are deleted on the 180-day schedule. To have one removed sooner, write to
  talero.dev@gmail.com with the date and the reason you selected.
- **Data already delivered to Google** as analytics or crash reports before the reset. Resetting
  severs the link to your device; write to talero.dev@gmail.com if you want a deletion request passed
  on.

Uninstalling the app removes everything in section 3 as well, but does not by itself reset the
Firebase identifiers or delete a filed report — use "Hapus data saya" first if that matters to
you.

## 9. Permissions

This is the complete list of what the app declares, including what its libraries add. Two of them
are ever shown to you as a request, and only at the moment you ask for the thing that needs them.

| Permission | Why |
|---|---|
| `INTERNET`, `ACCESS_NETWORK_STATE` | the AI writer, ads, Firebase |
| `POST_NOTIFICATIONS` | occasion reminders — **requested**, and only when you turn them on |
| `WRITE_EXTERNAL_STORAGE` (Android 9 and below only) | "Simpan ke galeri" on old versions — **requested** there, and only when you press Save; on Android 10 and above, saving needs no permission and none is asked for |
| `WAKE_LOCK`, `com.google.android.c2dm.permission.RECEIVE` | how Android delivers a push message; added by Cloud Messaging, never requested from you |
| `com.google.android.gms.permission.AD_ID`, `ACCESS_ADSERVICES_AD_ID`, `ACCESS_ADSERVICES_ATTRIBUTION` | the advertising identifier and its Privacy Sandbox replacements, added by the ads SDK |
| `READ_BASIC_PHONE_STATE` | network type, used by Google's measurement SDK |
| `READ_GSERVICES`, `BIND_GET_INSTALL_REFERRER_SERVICE` | signature-level permissions to talk to Google Play services; they grant no access to anything on your device |
| `com.talero.ucapin.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION` | generated by AndroidX for the app's own internal broadcasts; it is not a permission on anything outside the app |

The app asks for **no** access to your photos, contacts, location, camera, microphone or call
state. Choosing a photo goes through Android's system Photo Picker, which needs no permission. The
one file permission is the legacy save-to-gallery path on Android 9 and below described above; it
is never used to read anything.

## 10. Children

Ucapin is **not directed to children**. Its target audience is 18 and over, and it is not part of
Google Play's Families programme. We do not knowingly collect anything from a child. If you
believe a child has used the app and something about them has reached us, write to
talero.dev@gmail.com and it will be deleted.

## 11. Your rights

Indonesia's **Undang-Undang No. 27 Tahun 2022 tentang Pelindungan Data Pribadi (UU PDP)** is the
law that primarily applies, since Indonesia is the market this app is made for. Under it you have
the right to know what is processed and why, to have it corrected, to have it deleted, to
withdraw consent, and to complain to the supervisory authority.

The app is available worldwide through Google Play, so where they apply to you, the same requests
are honoured under the **GDPR** (EU/EEA and the UK), the **LGPD** (Brazil) and the **KVKK**
(Türkiye): access, rectification, erasure, restriction, objection, portability, and withdrawal of
consent at any time without affecting what was lawful before.

In practice, most of these you can exercise yourself: "Hapus data saya" is erasure, and "Opsi
privasi iklan" is withdrawal of consent. For anything else, write to talero.dev@gmail.com. We answer
within 30 days.

**Legal bases**, where a law requires one to be named: consent for advertising, analytics and the
identifiers behind them; performance of the service you asked for when you use the AI writer;
legitimate interest in keeping the app working and safe for crash reporting and for reviewing
reported AI output.

**International transfers.** Google processes the data described here on infrastructure that may
be outside your country, including the United States, under the terms linked in section 6.

## 12. Security

Traffic is encrypted in transit; the app disables cleartext HTTP entirely and trusts only the
system certificate store. App data is in the app's private storage, which Android isolates from
other apps. The only part of Ucapin that another app can reach is its launcher screen; the
handful of other externally visible components come from Google's and Android's own libraries and
are each protected by a signature-level or system permission. Images are shared with other apps
through a `FileProvider` that grants access to one file at a time. Release builds strip their
logs, and the app never logs what you typed.

## 13. AI-generated content

The AI writer produces suggestions, not facts. They can be wrong, tactless or unsuitable for the
person you are sending them to — read one before you use it. The app tells you which text came
from AI with a "Dibuat dengan AI" chip, and gives every suggestion a flag icon so you can report
it without leaving the app. Reports are reviewed; see sections 4.2 and 7. The model is instructed
never to quote or paraphrase scripture, never to produce political, sexual, hateful or violent
content, and to treat everything you type as data rather than as instructions; a filter in the
app also drops suggestions that fail its own check.

## 14. Changes to this policy

Material changes will be announced in the app before they take effect. The effective date at the
top of this page always says which version is current, and the previous versions are in the
project's version control history.

## 15. Contact

**Data controller:** Talero
**Privacy contact:** talero.dev@gmail.com

---

# Kebijakan Privasi (Bahasa Indonesia) — Ucapin

**Aplikasi:** Ucapin: Kartu Ucapan & Status (`com.talero.ucapin`)
**Pengembang:** Talero
**Kontak:** talero.dev@gmail.com
**Berlaku sejak:** 10 November 2026
**Versi:** 1.0, untuk aplikasi versi 1.0.0 (versionCode 10000)

> Ini teks bahasa Indonesia dan inilah yang berlaku bagi pengguna di Indonesia. Teks bahasa
> Inggris diterbitkan bersamanya di https://github.com/talero-dev/ucapin-privacy-policy.

## 1. Ringkasnya

Ucapin membuat kartu ucapan dan status. Tidak ada akun dan tidak ada pendaftaran. Semua yang Anda
buat — kartu, kata-kata yang Anda ketik di atasnya, foto yang Anda pasang sebagai latar — tetap
berada di ponsel Anda.

Paling banyak lima hal yang keluar dari ponsel Anda:

1. **Isian pada penulis AI**, saat Anda menekan "Tulis dengan AI", dikirim ke Google untuk
   menghasilkan tiga variasi ucapan.
2. **Variasi yang Anda laporkan** lewat ikon bendera, beserta alasan dan komentar opsional Anda,
   disimpan agar bisa kami tinjau.
3. **Data penggunaan dan laporan kerusakan** yang dikumpulkan SDK Firebase milik Google.
4. **Permintaan iklan**, ditangani Google AdMob, setelah Anda menjawab formulir persetujuan.
5. **Lalu lintas layanan rutin**: pengambilan setelan pada bagian 4.5, pendaftaran push pada
   bagian 4.6, dan ajakan ulasan serta pembaruan dari Google Play pada bagian 4.8. Tidak satu pun
   membawa apa yang Anda buat.

Kami tidak menjual data Anda. Kami tidak meminta kontak, lokasi, mikrofon, atau kamera Anda. Kami
tidak punya server sendiri: satu-satunya pihak ketiga yang terlibat adalah Google, dalam peran
yang dijelaskan di bagian 6.

## 2. Siapa kami

Ucapin diterbitkan oleh **Talero**, yang bertindak sebagai pengendali data untuk
pemrosesan yang dijelaskan di sini. Silakan tulis ke **talero.dev@gmail.com** untuk pertanyaan apa pun
tentang kebijakan ini, untuk menanyakan data apa yang disimpan tentang Anda, atau untuk meminta
penghapusan.

## 3. Yang tetap di perangkat Anda

Tidak satu pun dari ini dikirim ke mana-mana. Semuanya tersimpan di penyimpanan privat aplikasi,
yang tidak bisa dibaca aplikasi lain.

| Apa | Disimpan di | Isinya |
|---|---|---|
| Kartu yang belum selesai (draf) | `ucapin.db`, tabel `drafts` | templat, momen, dan isi kartu dalam bentuk JSON — termasuk setiap kata yang Anda ketik |
| Favorit | `ucapin.db`, tabel `favourites` | templat, pesan, dan momen yang Anda tandai |
| Riwayat ekspor | `ucapin.db`, tabel `exports` | format, templat, lokasi berkas, dan waktu setiap kartu yang Anda simpan atau bagikan |
| Templat yang dibuka lewat iklan berhadiah | `ucapin.db`, tabel `unlocked_templates` | id templat dan waktu terbukanya |
| Foto yang Anda pasang di kartu | `filesDir/photos/<uuid>.jpg` | salinan gambar pilihan Anda, diperkecil ke 2160 px pada sisi terpanjang |
| Pengaturan | DataStore `ucapin_settings` | tema, warna dinamis, getaran, pengingat, momen pilihan Anda, status onboarding |
| Jatah AI | DataStore `ucapin_ai_quota` | berapa kali Anda memakai jatah gratis hari ini, dan kredit dari iklan berhadiah |
| Waktu iklan | DataStore `ucapin_ad_counters` | kapan iklan layar penuh terakhir tampil, kapan iklan berhadiah terakhir tampil |
| Waktu ulasan | DataStore `ucapin_review` | kapan ajakan memberi ulasan terakhir tampil |

Tiga catatan.

**Pemilih foto.** Memilih foto memakai Photo Picker bawaan Android, dan itulah sebabnya aplikasi
ini tidak meminta izin apa pun untuk membaca gambar Anda — tidak `READ_MEDIA_IMAGES`, tidak yang
lain. Gambar yang dipilih disalin ke folder aplikasi
supaya kartu Anda tidak rusak ketika izin baca sementaranya habis; salinan itu dihapus saat Anda
menghapus draf yang memakainya, saat Anda memakai "Hapus data saya", atau oleh penyapuan pada
start berikutnya yang membuang setiap foto yang tidak lagi dirujuk draf mana pun.

**Papan klip.** "Salin teks" menaruh pesan di papan klip Android. Itu fitur sistem; setelah itu
teksnya di luar kendali aplikasi dan mengikuti perilaku papan klip Android sendiri.

**Cadangan.** Jika Auto Backup Android aktif, Android menyalin `ucapin.db` dan berkas DataStore
aplikasi — pengaturan Anda, jatah AI, dan stempel waktu ulasan — ke akun Google Anda sendiri,
terenkripsi, dengan ketentuan cadangan milik Google. Tiga hal tidak ikut dicadangkan: foto yang
Anda impor, waktu iklan beserta status persetujuan, dan pengenal Firebase. Ini dilakukan Android,
bukan aplikasi, dan bisa Anda matikan di pengaturan perangkat.

## 4. Apa yang dikirim, dan kapan

Bagian 4.1 sampai 4.6 menjelaskan layanan Google Firebase. Bagian-bagian itu hanya berlaku bila
layanan tersebut diaktifkan di aplikasi; bila tidak, tidak ada yang dijelaskan di bagian itu yang
dikirim, dan penulis AI memakai pustaka pesan bawaan aplikasi.

### 4.1 Penulis AI

Menekan "Buat" pada penulis AI mengirim permintaan ke **Firebase AI Logic**, yang memanggil
**Gemini Developer API** milik Google. Permintaan itu berisi tepat data berikut, tidak lebih:

- momen (id dan nama Inggrisnya) serta apakah momen itu bersifat duka;
- hubungan yang Anda pilih (sahabat, orang tua, rekan kerja, dan seterusnya);
- nama penerima, jika Anda mengisinya — maksimal 30 karakter;
- nama Anda sendiri, jika Anda mengisinya — maksimal 30 karakter;
- nada dan panjang yang Anda pilih;
- bahasa yang Anda minta;
- catatan tambahan, jika Anda menulisnya — maksimal 120 karakter.

Nilai-nilai itu dikirim di dalam blok data JSON, dengan instruksi yang memberi tahu model bahwa
isinya adalah data. Sebelum dikirim, aplikasi membersihkan karakter kontrol, pagar kode, dan
kalimat berbentuk instruksi dari ketiga isian bebas.

**Kata-kata yang Anda ketik di editor kartu tidak pernah dikirim ke penulis AI maupun ke mana
pun.** Begitu juga foto di belakang kartu Anda dan gambar jadinya.

Setiap permintaan membawa token **Firebase App Check** yang dihasilkan Google Play Integrity,
yang menyatakan bahwa permintaan itu datang dari pemasangan asli aplikasi ini. Token itu tidak
memuat informasi apa pun tentang Anda.

Google memproses permintaan ini sebagai penyedia layanan kami, berdasarkan Firebase Data
Processing and Security Terms dan ketentuan tambahan Gemini API. Ucapin tidak menyimpan salinan
permintaan maupun teks yang dihasilkan; ketiga variasi hanya ada di memori ponsel Anda sampai
Anda meninggalkan layar itu.

### 4.2 Melaporkan hasil AI

Setiap variasi punya ikon bendera. Melaporkan satu variasi menyimpan satu catatan di **Cloud
Firestore** milik Google, pada koleksi `ai_reports`, yang berisi:

| Kolom | Isinya |
|---|---|
| `createdAt` | stempel waktu dari server |
| `reason` | salah satu dari `tidak_pantas`, `menyinggung_sara`, `konten_seksual`, `informasi_keliru`, `lainnya` |
| `comment` | komentar opsional Anda, maksimal 200 karakter — tidak ikut jika dikosongkan |
| `occasion`, `tone`, `language` | bentuk permintaan yang menghasilkan teks itu |
| `modelName`, `appVersion` | model dan versi aplikasi yang menghasilkannya |
| `text` | teks yang dilaporkan, maksimal 4000 karakter |
| `textHash` | SHA-256 dari teks itu, supaya laporan kembar bisa dikelompokkan |

**Catatan itu tidak memuat nama penerima, nama Anda, maupun catatan tambahan Anda**, dan tidak
memuat apa pun yang lain dari aplikasi.

Untuk menulisnya, aplikasi masuk ke **Firebase Authentication** secara anonim — akun tanpa email,
tanpa kata sandi, dan tanpa profil, yang dibuat pertama kali saat Anda mengirim laporan dan tidak
sebelumnya. Pengenal akun anonim itu tidak ditulis ke dalam laporan.

Aturan keamanan koleksi itu mengizinkan satu hal saja: membuat laporan. Tidak ada klien mana pun,
termasuk aplikasi ini, yang bisa membaca, mengubah, atau menghapus isinya.

**Masa simpan: 180 hari.** Laporan ditinjau lalu dihapus.

### 4.3 Analitik

Aplikasi mengirim peristiwa penggunaan ke **Google Analytics for Firebase**. Daftar peristiwanya
ditetapkan di kode sumber aplikasi dan tidak bisa ditambah saat aplikasi berjalan. Daftarnya:

`screen_view`, `onboarding_complete`, `settings_change`, `consent_status`, `ad_request`,
`ad_loaded`, `ad_failed`, `ad_shown`, `ad_reward_earned`, `notification_opt_in`,
`review_prompt_shown`, `update_prompt_shown`, `occasion_view`, `template_open`, `editor_action`
(disampel), `ai_generate`, `ai_variant_used`, `ai_report`, `card_export`, `share`,
`message_copy`, `reminder_opt_in`.

Parameternya berupa id momen, id templat, nama format, nilai enum, rentang waktu, dan angka.
**Tidak ada satu peristiwa pun yang membawa teks yang Anda ketik, nama, foto, atau isi kartu.**
`editor_action`, misalnya, mencatat bahwa suntingan bertipe "text", "sticker", atau "photo" —
bukan isi suntingannya.

Empat properti pengguna disetel: `ui_theme`, `font_scale_bucket`, `app_language`,
`consent_state`.

Analitik dikaitkan pada pengenal app-instance Firebase, bukan pada diri Anda. Pengumpulan oleh
Google sendiri juga mencakup informasi perangkat dan aplikasi; lihat dokumentasi Google yang
ditautkan di bagian 6.

Penyimpanan analitik, penyimpanan iklan, data pengguna untuk iklan, dan personalisasi iklan
semuanya disetel dari jawaban Anda pada formulir persetujuan (bagian 5). Jika Anda menolak,
keempatnya disetel ke "ditolak".

### 4.4 Laporan kerusakan

**Hanya pada build rilis.** Build debug dan build QA internal tidak mengirim apa pun. **Firebase
Crashlytics** menerima jejak kesalahan, model perangkat, versi Android, pengenal instalasi
Firebase, dan tiga kunci khusus: layar yang sedang Anda buka (`route`), bahasa aplikasi
(`app_language`), dan ringkasan status aplikasi tanpa isi (`feature_state`, misalnya "3 draf, AI
aktif, pengingat mati"). Isi kartu dan teks yang Anda ketik tidak pernah dilampirkan pada laporan
kerusakan.

### 4.5 Konfigurasi jarak jauh

**Firebase Remote Config** mengambil flag dan batasan fitur aplikasi — apakah iklan aktif, nama
model AI dan jatahnya, templat unggulan, kalender hari besar. Pengambilan itu mengidentifikasi
instalasi ke Firebase; ia tidak mengirim apa pun tentang apa yang Anda buat.

### 4.6 Notifikasi

Pengingat untuk momen pilihan Anda dijadwalkan **di perangkat Anda** oleh WorkManager milik
Android. Tidak ada server yang terlibat dan daftar momen Anda tidak diunggah.

Terpisah dari itu, **Firebase Cloud Messaging** dipakai untuk pengumuman musiman. Aplikasi
berlangganan dua topik: `all`, dan `lang_` diikuti bahasa tampilan Anda (`lang_id`, `lang_en`).
Berlangganan topik memberi tahu Firebase token pendaftaran aplikasi dan nama topiknya, tidak lebih.
Pesan dikirim ke semua pelanggan topik; pesan itu tidak dialamatkan kepada Anda.

Notifikasi memerlukan izin `POST_NOTIFICATIONS` pada Android 13 ke atas. Aplikasi memintanya saat
Anda menyalakan pengingat, tidak pernah pada pembukaan pertama, dan tetap berjalan normal jika
Anda menolak.

### 4.7 Iklan

Aplikasi menampilkan iklan lewat **Google Mobile Ads SDK** (AdMob). Permintaan iklan dikirim ke
Google dan dapat melibatkan pengenal iklan Android atau penggantinya di Privacy Sandbox, perkiraan
lokasi Anda yang diturunkan dari alamat IP, serta informasi tentang perangkat dan aplikasi. Google
memakainya untuk memilih dan mengukur iklan. Lihat dokumentasi periklanan Google di bagian 6.

Sebelum iklan diminta, **User Messaging Platform** (UMP) milik Google menentukan — berdasarkan
lokasi Anda — apakah formulir persetujuan diperlukan, lalu menampilkannya. Tidak ada iklan yang
diminta sampai UMP menyatakan iklan boleh diminta. Jika formulir berlaku untuk Anda, Anda bisa
membukanya kembali kapan saja: **Pengaturan → Opsi privasi iklan**.

Iklan tidak ditampilkan saat onboarding, di Pengaturan, di layar privasi atau hukum, dan saat
Anda sedang mengetik di editor.

### 4.8 Google Play

**Ulasan dalam aplikasi** dan **pembaruan dalam aplikasi** memakai pustaka milik Google Play
sendiri. Ketika aplikasi meminta Play menampilkan ajakan memberi ulasan atau ajakan memperbarui,
Play yang menangani interaksinya; aplikasi hanya diberi tahu bahwa alurnya selesai, tidak pernah
apa yang Anda tulis dalam ulasan.

## 5. Persetujuan dan pilihan Anda

| Anda ingin | Di mana |
|---|---|
| Mengubah persetujuan iklan | Pengaturan → Opsi privasi iklan (tampil bila formulir berlaku untuk Anda) |
| Menghentikan pengingat | Pengaturan → Pengingat, atau pengaturan notifikasi Android untuk Ucapin |
| Menyetel ulang pengenal iklan Android | Setelan Android → Privasi → Iklan |
| Menghapus semuanya | Pengaturan → Hapus data saya (bagian 8) |
| Bertanya atau mengajukan permintaan | talero.dev@gmail.com |

Menolak persetujuan tidak mematikan aplikasi. Penulis AI, templat, editor, dan berbagi tetap
berjalan sama; yang berubah hanya personalisasi iklan dan sinyal analitik.

## 6. SDK pihak ketiga dan apa yang dilihat masing-masing

Semua SDK dalam aplikasi yang dikirim ke pengguna yang bisa mengirim sesuatu keluar dari perangkat
tercantum di sini, beserta versinya. Google adalah satu-satunya pihak ketiga.

| SDK | Versi | Yang dilihatnya |
|---|---|---|
| Google Mobile Ads (Next-Gen) | 1.5.0 | permintaan iklan; pengenal iklan atau padanannya di Privacy Sandbox; perkiraan lokasi dari IP; informasi perangkat dan aplikasi; interaksi iklan |
| User Messaging Platform (UMP) | 4.0.0 | lokasi Anda untuk menentukan formulir persetujuan mana yang berlaku, dan jawaban Anda atasnya |
| Google Analytics for Firebase | Firebase BoM 34.19.0 | daftar peristiwa tetap di bagian 4.3; empat properti pengguna; id app-instance; informasi perangkat dan aplikasi |
| Firebase Crashlytics | Firebase BoM 34.19.0 | kerusakan pada build rilis: jejak kesalahan, model perangkat, versi OS, id instalasi, tiga kunci khusus |
| Firebase Remote Config | Firebase BoM 34.19.0 | pengambilan konfigurasi itu sendiri dan id instalasi |
| Firebase Cloud Messaging | Firebase BoM 34.19.0 | token pendaftaran dan dua topik `all` serta `lang_<kode>` |
| Firebase Installations | Firebase BoM 34.19.0 | id instalasi yang menjadi kunci layanan-layanan di atas |
| Firebase AI Logic (Gemini Developer API) | Firebase BoM 34.19.0 | isian penulis AI yang dirinci di bagian 4.1 |
| Firebase App Check + Play Integrity | Firebase BoM 34.19.0 | pernyataan keaslian perangkat untuk setiap permintaan AI; tidak ada informasi tentang Anda |
| Cloud Firestore | Firebase BoM 34.19.0 | satu dokumen laporan per hasil yang dilaporkan, seperti dirinci di bagian 4.2 |
| Firebase Authentication (anonim) | Firebase BoM 34.19.0 | akun tanpa data pribadi apa pun, dibuat hanya saat Anda mengirim laporan pertama |
| Google Play in-app review | 2.0.2 | bahwa alur ulasan diminta |
| Google Play in-app update | 2.1.0 | bahwa alur pembaruan diminta |

Penjelasan dari Google sendiri:

- Kebijakan Privasi Google — https://policies.google.com/privacy
- Cara Google menggunakan informasi dari situs atau aplikasi yang memakai layanan kami —
  https://policies.google.com/technologies/partner-sites
- Privasi dan keamanan di Firebase — https://firebase.google.com/support/privacy
- Firebase Data Processing and Security Terms —
  https://firebase.google.com/terms/data-processing-terms
- Pengaturan privasi AdMob dan Android — https://developers.google.com/admob/android/privacy
- Firebase AI Logic — https://firebase.google.com/docs/ai-logic
- Ketentuan layanan tambahan Gemini API — https://ai.google.dev/gemini-api/terms
- Kebijakan penggunaan terlarang AI generatif —
  https://policies.google.com/terms/generative-ai/use-policy
- Firebase App Check — https://firebase.google.com/docs/app-check
- Play Integrity API — https://developer.android.com/google/play/integrity

## 7. Masa simpan

| Data | Disimpan selama |
|---|---|
| Semua yang ada di bagian 3 | sampai Anda menghapusnya, atau sampai aplikasi dicopot |
| Gambar hasil ekspor di cache aplikasi | dibersihkan otomatis; cache berumur pendek dan Android bisa membersihkannya kapan saja |
| Gambar yang Anda simpan ke galeri | milik Anda — aplikasi tidak mengelolanya lagi setelah tersimpan |
| Hasil AI yang dilaporkan (bagian 4.2) | **180 hari**, lalu dihapus |
| Permintaan dan jawaban penulis AI | tidak kami simpan; masa simpan di sisi Google mengikuti ketentuan yang ditautkan di bagian 6 |
| Peristiwa analitik | sesuai setelan retensi Analytics proyek, yang ditentukan pengembang di konsol Firebase |
| Laporan kerusakan | sesuai masa simpan Crashlytics |

## 8. Menghapus data Anda

**Pengaturan → Hapus data saya**, lalu konfirmasi. Dalam satu tindakan, ia menghapus:

- setiap draf, favorit, baris riwayat ekspor, dan templat terbuka di `ucapin.db`;
- setiap foto yang Anda impor;
- jatah AI hari ini dan kredit dari iklan berhadiah;
- stempel waktu ajakan memberi ulasan;
- pengaturan Anda: tema, warna dinamis, getaran, pengingat, dan momen pilihan;
- bila Firebase tersambung: ia menyetel ulang data Google Analytics, yang membuang peristiwa
  dalam antrean dan membuat pengenal app-instance baru, lalu menghapus pengenal instalasi
  Firebase — setelah itu Remote Config dan Cloud Messaging melihat perangkat Anda sebagai
  perangkat baru;
- status persetujuan iklan, sehingga formulir persetujuan ditampilkan lagi.

Onboarding sengaja tidak ditampilkan ulang. **Bahasa aplikasi tidak disetel ulang**: itu setelan
per-aplikasi milik Android, bukan milik kami, dan diubah lewat Pengaturan → Bahasa atau lewat
setelan Android untuk Ucapin.

Dua hal yang tidak bisa dijangkau tindakan ini, kami sebutkan terus terang:

- **Laporan yang sudah Anda kirim.** Aturan keamanan pada `ai_reports` menolak penghapusan dari
  klien mana pun, dan itulah yang membuat sebuah laporan menjadi bukti, bukan sesuatu yang bisa
  diubah belakangan. Laporan dihapus mengikuti jadwal 180 hari. Untuk menghapusnya lebih cepat,
  tulis ke talero.dev@gmail.com dengan menyebutkan tanggal dan alasan yang Anda pilih.
- **Data yang sudah sampai ke Google** sebagai analitik atau laporan kerusakan sebelum penyetelan
  ulang. Penyetelan ulang memutus kaitannya dengan perangkat Anda; tulis ke talero.dev@gmail.com jika
  Anda ingin permintaan penghapusan diteruskan.

Mencopot aplikasi juga menghapus semua yang ada di bagian 3, tetapi dengan sendirinya tidak
menyetel ulang pengenal Firebase dan tidak menghapus laporan yang sudah dikirim — pakai "Hapus
data saya" lebih dulu bila hal itu penting bagi Anda.

## 9. Izin

Ini daftar lengkap izin yang dideklarasikan aplikasi, termasuk yang ditambahkan pustakanya. Dua di
antaranya pernah ditampilkan kepada Anda sebagai permintaan, dan hanya pada saat Anda meminta hal
yang memerlukannya.

| Izin | Untuk apa |
|---|---|
| `INTERNET`, `ACCESS_NETWORK_STATE` | penulis AI, iklan, Firebase |
| `POST_NOTIFICATIONS` | pengingat momen — **diminta**, dan hanya saat Anda menyalakannya |
| `WRITE_EXTERNAL_STORAGE` (hanya Android 9 ke bawah) | "Simpan ke galeri" di versi lama — **diminta** di sana, dan hanya saat Anda menekan Simpan; pada Android 10 ke atas menyimpan tidak butuh izin dan tidak ada yang diminta |
| `WAKE_LOCK`, `com.google.android.c2dm.permission.RECEIVE` | cara Android mengantar pesan push; ditambahkan Cloud Messaging, tidak pernah diminta kepada Anda |
| `com.google.android.gms.permission.AD_ID`, `ACCESS_ADSERVICES_AD_ID`, `ACCESS_ADSERVICES_ATTRIBUTION` | pengenal iklan dan penggantinya di Privacy Sandbox, ditambahkan SDK iklan |
| `READ_BASIC_PHONE_STATE` | jenis jaringan, dipakai SDK pengukuran Google |
| `READ_GSERVICES`, `BIND_GET_INSTALL_REFERRER_SERVICE` | izin tingkat tanda tangan untuk berbicara dengan layanan Google Play; keduanya tidak memberi akses ke apa pun di perangkat Anda |
| `com.talero.ucapin.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION` | dibuat AndroidX untuk siaran internal aplikasi sendiri; bukan izin atas apa pun di luar aplikasi |

Aplikasi **tidak** meminta akses ke foto, kontak, lokasi, kamera, mikrofon, atau status telepon
Anda. Memilih foto dilakukan lewat Photo Picker bawaan Android, yang tidak memerlukan izin. Satu-
satunya izin berkas adalah jalur lama simpan-ke-galeri pada Android 9 ke bawah yang dijelaskan di
atas, dan itu tidak pernah dipakai untuk membaca apa pun.

## 10. Anak-anak

Ucapin **tidak ditujukan untuk anak-anak**. Sasaran penggunanya berusia 18 tahun ke atas dan
aplikasi ini tidak mengikuti program Families di Google Play. Kami tidak dengan sengaja
mengumpulkan apa pun dari anak. Jika Anda yakin seorang anak memakai aplikasi ini dan ada datanya
yang sampai kepada kami, tulis ke talero.dev@gmail.com dan data itu akan dihapus.

## 11. Hak Anda

Hukum yang terutama berlaku adalah **Undang-Undang No. 27 Tahun 2022 tentang Pelindungan Data
Pribadi (UU PDP)**, karena Indonesia adalah pasar yang dituju aplikasi ini. Berdasarkan
undang-undang itu Anda berhak mengetahui data apa yang diproses dan untuk apa, meminta
pembetulan, meminta penghapusan, menarik persetujuan, dan mengajukan keberatan kepada lembaga
pengawas.

Aplikasi ini tersedia di seluruh dunia melalui Google Play, jadi sejauh berlaku bagi Anda,
permintaan yang sama kami layani berdasarkan **GDPR** (Uni Eropa/EEA dan Inggris), **LGPD**
(Brasil), dan **KVKK** (Turki): akses, pembetulan, penghapusan, pembatasan, keberatan,
portabilitas, dan penarikan persetujuan kapan saja tanpa memengaruhi keabsahan pemrosesan
sebelumnya.

Sebagian besar hak itu bisa Anda jalankan sendiri: "Hapus data saya" adalah penghapusan, dan
"Opsi privasi iklan" adalah penarikan persetujuan. Untuk hal lain, tulis ke talero.dev@gmail.com. Kami
menjawab dalam 30 hari.

**Dasar pemrosesan**, bila suatu hukum mengharuskannya disebut: persetujuan untuk iklan, analitik,
dan pengenal di baliknya; pelaksanaan layanan yang Anda minta ketika Anda memakai penulis AI;
kepentingan yang sah untuk menjaga aplikasi tetap berjalan dan aman, yaitu pada laporan kerusakan
dan peninjauan hasil AI yang dilaporkan.

**Transfer ke luar negeri.** Google memproses data yang dijelaskan di sini pada infrastruktur yang
dapat berada di luar negara Anda, termasuk Amerika Serikat, dengan ketentuan yang ditautkan di
bagian 6.

## 12. Keamanan

Lalu lintas data dienkripsi saat dikirim; aplikasi mematikan HTTP tanpa enkripsi sepenuhnya dan
hanya mempercayai sertifikat sistem. Data aplikasi berada di penyimpanan privat aplikasi, yang
dipisahkan Android dari aplikasi lain. Satu-satunya bagian Ucapin yang bisa dijangkau aplikasi
lain adalah layar peluncurnya; beberapa komponen lain yang terlihat dari luar berasal dari pustaka
milik Google dan Android sendiri, dan masing-masing dilindungi izin tingkat tanda tangan atau izin
sistem. Gambar dibagikan ke aplikasi lain melalui `FileProvider` yang memberi akses satu berkas
dalam satu waktu. Build rilis membuang log-nya, dan aplikasi tidak pernah mencatat apa yang Anda
ketik.

## 13. Konten buatan AI

Penulis AI menghasilkan saran, bukan fakta. Hasilnya bisa keliru, kurang pas, atau tidak cocok
untuk orang yang Anda tuju — baca dulu sebelum dipakai. Aplikasi menandai teks buatan AI dengan
cip "Dibuat dengan AI", dan setiap variasi punya ikon bendera supaya Anda bisa melaporkannya tanpa
meninggalkan aplikasi. Laporan ditinjau; lihat bagian 4.2 dan 7. Model diinstruksikan untuk tidak
pernah mengutip atau memparafrasakan ayat maupun hadis, tidak menghasilkan konten politik,
seksual, kebencian, atau kekerasan, dan memperlakukan semua isian Anda sebagai data, bukan sebagai
perintah; sebuah penyaring di dalam aplikasi juga membuang variasi yang tidak lolos pemeriksaannya
sendiri.

## 14. Perubahan kebijakan ini

Perubahan yang berarti akan diumumkan di dalam aplikasi sebelum berlaku. Tanggal berlaku di bagian
atas halaman ini selalu menunjukkan versi yang sedang berlaku, dan versi-versi sebelumnya tersimpan
di riwayat version control proyek.

## 15. Kontak

**Pengendali data:** Talero
**Kontak privasi:** talero.dev@gmail.com
