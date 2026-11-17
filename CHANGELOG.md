# Changelog

## v1.18-tsc.0.4 (2026-11-17)
- WiFi točka za postavljanje upaljena tipkom (5 s) ili naredbom `portal` sama se **gasi nakon 10 minuta** ako nitko nije spojen na nju. Tako stranica za postavljanje, koja na toj mreži ne traži admin lozinku, ne ostaje otvorena. Dok nije postavljen WiFi, točka ostaje uključena.

## v1.18-tsc.0.3 (2026-11-17)
- Držanje tipke **USER** na Wio pločici **5 sekundi** dok node radi pali WiFi točku za postavljanje `MC-TS-XXXX` (lozinka `password`). Ostaje uključena do restarta.
- Ispravak: serijska naredba `portal` više ne gasi pristupnu točku odmah ako je node spojen na WiFi.

## v1.18-tsc.0.2 (2026-11-17)
- Web sučelje: gumb **Skeniraj** pokraj SSID-a prikaže WiFi mreže u blizini (jačina signala, zaštita); klik odabere mrežu. Ručni upis i dalje radi.
- Jednostavna adresa po imenu: **http://<naziv>.local/** kod kuće (mDNS) i **http://<naziv>/** preko Tailscalea (MagicDNS). Naziv je ime uređaja na tailnetu, npr. "TS test" → `ts-test.local`. Isto ime vidi i router.
- Nadogradnja s GitHuba se u web sučelju prikazuje samo kad je uređaj spojen na WiFi s internetom; bez interneta ostaje ručni upload `_ota.bin`.
- Ispravak: flash datoteka za prvu instalaciju (bootloader u DIO načinu).

## v1.18-tsc.0.1 (2026-11-16) – prvo izdanje
- MeshCore companion (osnova v1.18.0) za XIAO ESP32-S3 + Wio-SX1262 preko WiFi/TCP-a (port 5000)
- Tailscale ugrađen u čvor: prijava auth ključem, automatsko ponovno spajanje nakon pada WiFi-ja
- Web sučelje HR/EN: SoftAP `MC-TS-XXXX` (captive portal) te tailnet/LAN uz admin lozinku
- Postavke: WiFi, Tailscale, naziv, lokacija, radio preseti, path hash, RX gain, multi-ACK,
  telemetrija, automatsko dodavanje kontakata, "port 5000 samo preko Tailscalea"
- Serial CLI za sve postavke
- Nadogradnja s GitHuba (potpisani firmware, stabilni i beta kanal) i ručni upload kroz portal;
  automatski povratak na staru verziju ako se nova ne spoji na WiFi u 2 minute
- Tvornički reset, uz opciju zadržavanja identiteta čvora
