# MeshCore Tailscale Companion

**Hrvatski** · [English](#english)

Firmware za MeshCore companion čvor **Seeed XIAO ESP32-S3 + Wio-SX1262** s ugrađenim
**Tailscale VPN-om**. Čvor se sam spoji na WiFi, prijavi na tvoj tailnet i dobije
100.x.x.x adresu. MeshCore aplikacija ga tada vidi s bilo kojeg mjesta (mobilni podaci,
tuđi WiFi), bez Raspberry Pi-ja, subnet routera, otvaranja portova i javne IP adrese.

## Preporučena aplikacija: MeshCore Go
Firmware i Android aplikacija **[MeshCore Go](https://github.com/petelash/MeshCore-Go)**
su dva dijela istog projekta i rade najbolje zajedno.

- **MeshCore Go** je potpuna MeshCore aplikacija (kanali, poruke, karta, upravljanje
  repeaterima) s **ugrađenim Tailscale VPN-om**, pa na mobitelu ne treba zaseban
  Tailscale app.
- **Ovaj firmware** stavlja Tailscale u sam companion čvor, pa kod kuće više ne treba
  nikakvo dodatno računalo: dovoljni su čvor, WiFi i napajanje.

Čvor ostaje kod kuće na mjestu gdje antena najbolje radi, a ti ga preko MeshCore Go
koristiš odakle god. Firmware radi i sa službenim MeshCore appom (TCP na
`100.x.x.x:5000`, uz Tailscale app na mobitelu).

## Značajke
- MeshCore companion (osnova v1.18.0) preko WiFi/TCP-a, port 5000
- Tailscale u samom čvoru: direktna veza ili DERP relej, automatsko ponovno spajanje
- Web sučelje (HR/EN):
  - na pristupnoj točki `MC-TS-XXXX` pri prvom pokretanju, uz USER tipku ili nakon
    3 minute bez WiFi-ja
  - na Tailscale i lokalnoj adresi, uz admin lozinku
- Postavke: WiFi, Tailscale, naziv, lokacija, radio preseti, path hash, RX gain,
  multi-ACK, telemetrija, automatsko dodavanje kontakata
- Opcija "port 5000 samo preko Tailscalea"
- Serial CLI za sve postavke
- Nadogradnja:
  - s ovog repoa (gumb u portalu ili `ota update`), samo **potpisani** firmware
  - automatski povratak na staru verziju ako se nova ne spoji na WiFi u 2 minute
- Tvornički reset, uz opciju zadržavanja identiteta čvora

## Instalacija jednim klikom
**👉 https://petelash.github.io/meshcore-tailscale-companion/** (Chrome ili Edge na računalu):
spoji XIAO USB-om, klikni **Instaliraj** i odaberi port. Adrese se postave same.

## Preuzimanje
**⬇️ [Preuzmi flash datoteku (.bin)](https://github.com/petelash/meshcore-tailscale-companion/raw/main/firmware/MeshCore-Tailscale-Companion_v1.18-tsc.0.1_XiaoS3WIO_merged.bin)**: prvo flashanje, adresa `0x0`.

Flashanje (Chrome ili Edge na računalu):
1. Otvori https://espressif.github.io/esptool-js/
2. Spoji XIAO USB kabelom. Ako se port ne pojavi: drži **BOOT**, kratko pritisni **RESET**, pusti BOOT.
3. **Connect** i odaberi port.
4. U polje *Flash Address* upiši **`0x0`** i odaberi preuzetu `.bin` datoteku.
5. **Program**, pričekaj kraj, pa pritisni **RESET**.
6. Na mobitelu se pojavi WiFi **MC-TS-XXXX** (lozinka `password`). Spoji se i otvori http://192.168.4.1/

Datoteku obavezno preuzmi preko gornjeg linka. Ne koristi "Source code (zip)" ni link na GitHub
stranicu datoteke: to nije firmware.

Sve datoteke su i u **[Releases](../../releases)**:
- `…_merged.bin`: prvo flashanje od adrese `0x0`
- `…_ota.bin`: nadogradnja kroz web portal (GitHub nadogradnju uređaj radi sam)

Upute za flashanje, prvo postavljanje, Tailscale ACL, CLI, test i poznata ograničenja:
**[docs/UPUTE.md](docs/UPUTE.md)**.

Promjene po verzijama: **[CHANGELOG](CHANGELOG.md)**.

Ovaj repozitorij sadrži samo **izdanja** (firmware datoteke) i upute. Izvorni kod nije javan.

## Licenca
MeshCore Tailscale Companion © 2026 **Marinko Vrbanić** (MeshCore Hrvatska), objavljen pod
**[MIT licencom](LICENSE)**. Smiješ ga slobodno koristiti, kopirati i dijeliti, uz uvjet
da zadržiš napomenu o autorskim pravima i tekst licence.
Firmware se daje **"takav kakav jest", bez ikakvog jamstva**. Autor ne odgovara za štetu
nastalu njegovom upotrebom.

Neslužbeni projekt, nije povezan s MeshCore projektom ni s tvrtkom Tailscale Inc. MeshCore
i Tailscale pripadaju svojim vlasnicima. Komponente otvorenog koda i njihove licence:
[THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).

---

## English
Firmware for the MeshCore companion node **Seeed XIAO ESP32-S3 + Wio-SX1262** with
**Tailscale VPN built in**. The node joins WiFi and your tailnet by itself, so the MeshCore
app reaches it from anywhere (`100.x.x.x:5000`), with no subnet router, port forwarding
or public IP.

Recommended app: **[MeshCore Go](https://github.com/petelash/MeshCore-Go)**, a full Android
MeshCore client with Tailscale built in. Firmware and app are two halves of the same project.

Features: bilingual setup portal (access point `MC-TS-XXXX`, password `password`, plus the
tailnet/LAN with an admin password), serial CLI, signed over-the-air updates from this
repository with automatic rollback.

Download from **[Releases](../../releases)**: `…_merged.bin` for the first flash at `0x0`,
`…_ota.bin` for web upload. Full guide (Croatian): [docs/UPUTE.md](docs/UPUTE.md).

This repository holds only the **releases** and documentation. The source code is not public.

© 2026 Marinko Vrbanić, MIT licence. Provided "as is", without warranty. Unofficial; not
affiliated with MeshCore or Tailscale Inc.
