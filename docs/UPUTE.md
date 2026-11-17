# MeshCore Tailscale Companion – upute

Firmware za **Seeed XIAO ESP32-S3 + Wio-SX1262**. Companion se spaja na WiFi i
prijavljuje na tvoj Tailscale tailnet. MeshCore app na mobitelu se tada spaja na
`100.x.x.x:5000` s bilo kojeg mjesta: kod kuće, na mobilnim podacima ili na tuđem WiFi-ju.

> ## 👉 [**Web flasher: instalacija i nadogradnja jednim klikom**](https://petelash.github.io/meshcore-tailscale-companion/)
> https://petelash.github.io/meshcore-tailscale-companion/ · Chrome ili Edge na računalu, XIAO spojen USB-om

> 📶 **Postavljanje nakon flashanja:** spoji se na WiFi **`MC-TS-XXXX`**, lozinka **`password`**,
> pa otvori **http://192.168.4.1/** (XXXX = zadnja 4 znaka MAC adrese uređaja).

> 📘 **Upute za početnike (PDF) s MeshCore Go aplikacijom:** [hrvatski](MeshCore-Tailscale-Companion_MeshCore-Go_upute_HR.pdf) · [English](MeshCore-Tailscale-Companion_MeshCore-Go_guide_EN.pdf)

Sadržaj:
1. [Što ti treba](#1-što-ti-treba)
2. [Tailscale auth ključ](#2-tailscale-auth-ključ)
3. [Flashanje](#3-flashanje)
4. [Prvo postavljanje (SoftAP portal)](#4-prvo-postavljanje-softap-portal)
5. [Spajanje appa](#5-spajanje-appa)
6. [Tailscale ACL: samo moj mobitel](#6-tailscale-acl-samo-moj-mobitel)
7. [Serial CLI](#7-serial-cli)
8. [Nadogradnja firmwarea (OTA)](#8-nadogradnja-firmwarea-ota)
9. [Test plan](#9-test-plan)
10. [Poznata ograničenja](#10-poznata-ograničenja)

---

## 1. Što ti treba
- XIAO ESP32-S3 + Wio-SX1262 kit s antenom (**nikad ne pali radio bez antene**)
- USB-C kabel s podatkovnim linijama
- Tailscale račun, s Tailscale appom na mobitelu
- WiFi mreža s pristupom internetu (2.4 GHz)

## 2. Tailscale auth ključ
1. Otvori https://login.tailscale.com/admin/settings/keys → **Generate auth key**.
2. Uključi **Reusable**. Tada se uređaj može ponovno registrirati nakon tvorničkog
   reseta ili `ts logout`.
3. Preporučeno: uključi **Tags** i odaberi npr. `tag:meshcore` (vidi ACL u točki 6).
   Tagirani uređaji ne istječu (nema key expiry) i ne vežu se uz tvoj osobni račun.
4. Kopiraj ključ (`tskey-auth-...`). Upisat ćeš ga u portal.

## 3. Flashanje

### Web flasher (preporučeno)
> ## 👉 [**Otvori web flasher**](https://petelash.github.io/meshcore-tailscale-companion/)
> https://petelash.github.io/meshcore-tailscale-companion/ · Chrome ili Edge na računalu

1. Spoji XIAO USB kabelom.
2. **Prva instalacija:** klikni **Instaliraj**, odaberi port, potvrdi brisanje i pričekaj kraj.
   **Nadogradnja preko USB-a:** klikni **Nadogradi (zadrži postavke)**. Postavke, Tailscale
   (ista 100.x adresa), kontakti i identitet ostaju.
3. Pritisni **RESET** na uređaju.

Adrese se postave same, pa ništa ne trebaš upisivati. Ako se port ne pojavi: drži **BOOT**,
kratko pritisni **RESET**, pusti BOOT i pokušaj ponovno.

### Ručno (rezerva)
Prvi put flashaj **merged** datoteku (bootloader, particije i aplikacija zajedno) od adrese `0x0`.

**esptool-js (Chrome/Edge):**
1. Otvori https://espressif.github.io/esptool-js/
2. Spoji XIAO preko USB-a. Ako se ne pojavi port: drži **BOOT**, pritisni **RESET**, pusti BOOT.
3. Baudrate 921600 → **Connect** → odaberi port.
4. Flash Address `0x0`, datoteka `MeshCore-Tailscale-Companion_<verzija>_XiaoS3WIO_merged.bin` → **Program**.
5. Kad završi, pritisni RESET.

**Preko esptoola:**
```bash
esptool.py --chip esp32s3 --baud 921600 write_flash 0x0 MeshCore-Tailscale-Companion_v1.18-tsc.0.3_XiaoS3WIO_merged.bin
```

> Particijska tablica nije ista kao kod standardnog MeshCore companiona ni MeshCore
> Tailscale Bridgea. Kod prelaska s drugog firmwarea prvi put flashaj merged datoteku.
> Postojeći MeshCore identitet i kontakti se tada ne prenose. Ako ih želiš zadržati,
> prije flashanja ih izvezi iz appa.

## 4. Prvo postavljanje (SoftAP portal)
Kad nema WiFi postavki, uređaj sam pali pristupnu točku:

| | |
|---|---|
| WiFi mreža | `MC-TS-XXXX` (XXXX = zadnja 4 znaka MAC adrese) |
| Lozinka | `password` |
| Adresa | http://192.168.4.1/ (većina mobitela je otvori sama) |

Portal se pali i:
- ako **držiš USER tipku** na Wio pločici **5 sekundi** dok uređaj radi (pristupna točka tada ostaje
  uključena do restarta), ili je držiš dok ukopčavaš napajanje,
- ako se uređaj **3 minute** ne može spojiti na WiFi (npr. promijenila se lozinka).
  Uređaj i dalje pokušava spojiti WiFi, a portal se gasi kad WiFi proradi.

U portalu upiši:
1. **WiFi i Tailscale**: SSID, WiFi lozinku i Tailscale auth ključ. Naziv uređaja na
   tailnetu je opcionalan; ako ga ostaviš praznim, izvodi se iz naziva čvora.
2. **Companion**: naziv čvora, lokaciju i **novu admin lozinku**.
   Zadana lozinka je `password`, a portal te upozorava dok je ne promijeniš.
3. **Radio**: odaberi preset regije (za Hrvatsku *EU/UK (Narrow)*). Postavke moraju
   odgovarati ostalim čvorovima u mreži.
4. **Spremi i restartaj**.

U polju za SSID možeš kliknuti **Skeniraj** i odabrati mrežu s popisa, ili upisati naziv ručno.

Nakon restarta uređaj je dostupan i **po imenu**: kod kuće **http://<naziv>.local/**, preko Tailscalea
**http://<naziv>/**. Naziv je ime uređaja na tailnetu (npr. naziv čvora "TS test" → `ts-test.local`).

Nakon restarta uređaj ispisuje na serialu (115200) svoju VPN adresu:
```
Tailscale: connected, VPN IP 100.101.42.17 (app: 100.101.42.17:5000, web: http://100.101.42.17/)
```
Adresu vidiš i u Tailscale admin konzoli i Tailscale appu na mobitelu.

Portal je kasnije dostupan na `http://100.x.x.x/` i na lokalnoj IP adresi uređaja.
Prijava: korisnik `admin`, lozinka je admin lozinka. Na SoftAP-u se ne traži prijava.

### Zadane postavke
| Postavka | Zadano |
|---|---|
| Path hash | 2 bajta |
| RX boosted gain | uključeno |
| Višestruki ACK | uključeno |
| Lokacija u advertu | ne dijeli |
| Telemetrija | osnovno: svi; lokacija i senzori: samo označeni kontakti |
| Auto-add kontakata | samo companioni, bez limita hopova, prepiši najstariji kad je popis pun |
| Port 5000 samo preko Tailscalea | isključeno |

## 5. Spajanje appa

### Koju IP adresu je Tailscale dodijelio?
Bilo koji od ovih načina:
- **Tailscale admin konzola** → https://login.tailscale.com/admin/machines: uređaj je
  na popisu pod nazivom s tailneta, a uz njega piše adresa `100.x.x.x`.
- **Tailscale app ili MeshCore Go na mobitelu**: popis uređaja na tailnetu.
- **Na računalu s Tailscaleom**: `tailscale status` (ili `tailscale ip <naziv-uređaja>`).
- **Serial** (USB, 115200): pri spajanju se ispiše
  `Tailscale: connected, VPN IP 100.x.x.x`, a naredba `status` je ispiše bilo kada.
- **Web portal** (lokalna IP adresa uređaja ili SoftAP): *Status → VPN IP*.
- **MagicDNS**: ako je u tailnetu uključen (zadano je), uređaj je dostupan i po imenu,
  npr. `naziv-uredaja` ili `naziv-uredaja.<tailnet>.ts.net`, pa IP ne moraš pamtiti.

Adresa ostaje ista kroz restarte i OTA nadogradnje. Mijenja se samo nakon
`ts logout` ili tvorničkog reseta, jer uređaj tada postaje novi čvor na tailnetu.

### Preporučeno: MeshCore Go
**[MeshCore Go](https://github.com/petelash/MeshCore-Go)** je Android MeshCore aplikacija
s ugrađenim Tailscaleom, napravljena kao par ovom firmwareu. Zaseban Tailscale app ne
treba: prijaviš se na tailnet u MeshCore Go i dodaš companion kao WiFi/TCP čvor na
`100.x.x.x:5000`.

### Službeni MeshCore app
1. Na mobitelu uključi Tailscale app (isti tailnet).
2. MeshCore app → dodaj uređaj → **TCP / WiFi**. Host `100.x.x.x`, port `5000`.
3. Spajanje radi i na mobilnim podacima.

Ako želiš da se app može spojiti **samo** preko Tailscalea (a ne iz lokalne mreže),
uključi *Sigurnost → Port 5000 samo preko Tailscalea*.

## 6. Tailscale ACL: samo moj mobitel

Važno je razumjeti kako ACL ovdje djeluje:
- Tailscale koordinacijski server šalje svakom uređaju samo one peerove s kojima mu ACL
  dopušta komunikaciju. Ako ACL dopušta pristup companionu samo tvom mobitelu, nijedan
  drugi uređaj na tailnetu ga ne vidi i ne može uspostaviti vezu prema njemu.
- **MicroLink (Tailscale klijent na ESP32) ne provodi filter portova.** Mobitel koji smije
  pristupiti companionu dolazi do svih portova na kojima uređaj sluša, a to su 5000 i 80.
  Ograničenje portova u ACL-u je zato dokumentacija namjere, a stvarna zaštita je popis
  peerova.

Primjer pravila (Admin konzola → **Access controls**). Zamijeni ime mobitela i e-mail:

```jsonc
{
  "tagOwners": {
    // tko smije dodijeliti tag companion uređajima
    "tag:meshcore": ["autogroup:admin"]
  },
  "hosts": {
    // tailnet IP tvog mobitela (Tailscale app → Ovaj uređaj)
    "moj-mobitel": "100.88.12.3"
  },
  "grants": [
    // mobitel -> companion: MeshCore app (5000) i web portal (80)
    { "src": ["moj-mobitel"], "dst": ["tag:meshcore"], "ip": ["tcp:5000", "tcp:80"] }

    // ... ostala tvoja pravila za druge uređaje, ali NE "*" -> "*" ili pravila
    //     koja uključuju tag:meshcore kao odredište
  ],
  "ssh": []
}
```

Napomene:
- Ako tvoja politika još sadrži zadano pravilo "svi prema svima" (`"src": ["*"], "dst": ["*"]`),
  companion vide svi uređaji. Ukloni ga ili ga suzi.
- Umjesto IP adrese mobitela možeš koristiti i korisnika (`"src": ["ti@primjer.com"]`).
  Tada companionu pristupaju svi tvoji uređaji, ne samo mobitel.
- Auth ključ mora biti tagiran s `tag:meshcore` (točka 2). Inače uređaj pripada tvom
  korisniku i pravilo s tagom ga ne pogađa.
- Nakon spremanja ACL-a promjena stiže do uređaja za nekoliko sekundi. Provjeri da drugi
  uređaj više ne vidi companion: `tailscale ping 100.x.x.x` s njega mora javiti grešku.

## 7. Serial CLI
USB serial, 115200 baud (npr. `pio device monitor`, Arduino Serial Monitor, PuTTY).
Upiši `help` za popis naredbi. Najvažnije:

```
status                       stanje WiFi-ja, Tailscalea, appa, firmwarea
wifi ssid <naziv>            wifi pwd <lozinka>
ts key <tskey-auth-...>      ts name <naziv>       ts logout [novi ključ]
admin pwd <lozinka>          tsonly on|off
name <naziv>                 loc <lat> <lon>       advloc on|off
radio 869.618 62.5 8 8       tx 22                 hash 2
rxgain on|off                multiack on|off
tele base|loc|env none|flagged|all
autoadd all|selective        autoadd types chat,repeater,room,sensor
autoadd overwrite on|off     autoadd hops <n>|none
ota check                    ota update            ota channel stable|beta
portal                       reboot                factory reset [keepid]
```
Većina postavki se primjenjuje nakon `reboot`. Tvornički reset traži da istu naredbu
pošalješ dvaput.

## 8. Nadogradnja firmwarea (OTA)

### S GitHuba (preporučeno)
- Portal → **Firmware → Provjeri nadogradnju**, pa **Nadogradi sada**.
- Ili na serialu `ota check` pa `ota update`.

Uređaj pita GitHub (`petelash/meshcore-tailscale-companion`) za zadnje izdanje. Na
beta kanalu gleda i pre-release izdanja. Preuzima `..._ota.bin` preko HTTPS-a i
provjerava **ECDSA potpis** autora, a datoteku bez ispravnog potpisa odbija. Postavke,
kontakti, identitet i Tailscale ostaju, a uređaj zadržava istu 100.x adresu.

### Preko USB-a, web flasherom
[Web flasher](https://petelash.github.io/meshcore-tailscale-companion/) → **Nadogradi (zadrži postavke)**.
Za uređaj koji nije na mreži. Sve postavke ostaju.

> Za nadogradnju **nemoj** koristiti gumb *Instaliraj* ni merged datoteku: one brišu
> postavke (Tailscale ključ i adresu, admin lozinku).

### Ručno, kroz portal
Portal → **Firmware → Učitaj datoteku firmwarea**, pa odaberi `..._ota.bin`.
Ovdje se provjerava samo da je riječ o ESP32-S3 firmwareu, ne i potpis. Učitavanje je
ionako zaštićeno admin lozinkom.

### Povratak na staru verziju
Nova verzija se pali "na probu". Ako se u **2 minute** ne spoji na WiFi, uređaj se sam
restartira u prethodnu verziju.

## 9. Test plan

| # | Test | Očekivano |
|---|---|---|
| 1 | Flash merged `.bin`, otvori serial monitor | `Setup portal: WiFi 'MC-TS-XXXX'...` |
| 2 | Spoji mobitel na `MC-TS-XXXX`, otvori portal | Captive portal se otvori sam (ili http://192.168.4.1/), bez prijave |
| 3 | Unesi WiFi, auth ključ, naziv, admin lozinku → Spremi | "Spremljeno. Uređaj se restartira..." |
| 4 | Serial nakon restarta | `WiFi: connected...`, pa `Tailscale: connected, VPN IP 100.x.x.x` (obično < 30 s) |
| 5 | `tailscale ping 100.x.x.x` s računala | odgovor `pong`; *via DERP* ili *via <ip>:<port>* (direktno) |
| 6 | `http://100.x.x.x/` u pregledniku | traži prijavu (`admin` / admin lozinka), zatim portal sa statusom *Spojeno* |
| 7 | `nc 100.x.x.x 5000` | TCP veza se otvori; status u portalu: *App: Spojen · 100.y.y.y* |
| 8 | MeshCore app preko **mobilnih podataka** (WiFi isključen), TCP `100.x.x.x:5000` | app se spoji, vidi kontakte i kanale |
| 9 | Pošalji poruku drugom čvoru preko LoRe | druga strana primi poruku, a app dobije ACK |
| 10 | Pošalji poruku s drugog čvora prema companionu | poruka stiže u app preko tailneta |
| 11 | Uključi *Port 5000 samo preko Tailscalea*, spoji app preko lokalne IP | veza se odbije; preko 100.x radi |
| 12 | Isključi ruter na 1 min, pa ga uključi | WiFi i Tailscale se same vrate (`Tailscale: WiFi back, rebinding`), a app se ponovno spoji |
| 13 | Ruter isključen > 3 min | pali se SoftAP `MC-TS-XXXX`, a nakon povratka WiFi-ja se gasi |
| 14 | `ota check` | `up to date` ili `new version ... available` |
| 15 | ACL iz točke 6, `tailscale ping` s drugog uređaja | ne dolazi do companiona |
| 16 | `factory reset keepid` (2x) | restart u SoftAP; javni ključ čvora isti kao prije |

## 10. Poznata ograničenja
- **Potrošnja:** WiFi je stalno uključen, bez štednog načina, jer štednja dodaje 0.3–2 s
  kašnjenja i gubitke paketa preko tunela. Računaj na oko 100–150 mA na 5 V. Uređaj je
  namijenjen stalnom napajanju, ne bateriji.
- **Ponovno spajanje:** nakon pada WiFi-ja ESP32 se sam ponovno spaja, a MicroLink
  ponovno veže sockete. Tailscale je obično ponovno gore za 5–30 s. Dok traje kratki
  prekid prema Tailscale koordinacijskom serveru (*Ponovno spajanje*), postojeće veze i
  dalje rade.
- **DERP latencija:** kad direktna UDP veza nije moguća (CGNAT na mobilnoj mreži,
  strogi NAT), promet ide preko Tailscale DERP releja (npr. Frankfurt). Tada dodaje
  20–100 ms i više. Za MeshCore app to nije problem, jer je LoRa ionako sporija za
  nekoliko redova veličine. U portalu, pod *Detalji → Veza s appom*, vidiš ide li
  veza direktno ili preko DERP-a.
- **ACL i portovi:** MicroLink ne provodi Tailscale filter portova (vidi točku 6).
  Pristup se ograničava popisom peerova.
- **Jedan klijent:** port 5000 prihvaća jednu vezu, a nova veza prekida staru, kao u
  standardnom MeshCore WiFi companionu.
- **Nema BLE-a:** companion radi samo preko WiFi/TCP-a.
- **Prvo pokretanje Tailscalea** (nova registracija) traje do ~1 min. Kasnija pokretanja
  su brža jer se ključevi i peerovi čuvaju u NVS-u.
- **GitHub API:** neautentificirano, 60 zahtjeva na sat po javnoj IP adresi. Za ručne
  provjere to je dovoljno.
- **Web OTA bez potpisa:** ručni upload kroz portal ne traži potpis (samo admin lozinku).
  GitHub OTA uvijek traži potpis.
- **Particije:** 2 × 3.2 MB za aplikaciju i 1.6 MB SPIFFS. Prelazak s/na standardni
  MeshCore firmware traži flash merged datoteke, a podaci se ne prenose.

