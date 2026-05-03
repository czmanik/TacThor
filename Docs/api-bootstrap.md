# TacThor – Bootstrap API

## 🎯 Účel

Bootstrap endpoint slouží k inicializaci aplikace po přihlášení.

Vrací všechna data potřebná pro:

- offline provoz
- nastavení aplikace
- řízení práv uživatele
- první zobrazení mapy

---

## 🔐 Login

### POST /auth/login

```json
{
  "loginCode": "ALFA10-CMD",
  "deviceId": "device-uuid",
  "appVersion": "0.1.0"
}
