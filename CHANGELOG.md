# Changelog

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
