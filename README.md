# Lendo Bem — APK via GitHub Actions

Este repositório compila o APK do Lendo Bem na nuvem do GitHub.
Você não precisa de espaço em disco nem RAM extra no seu computador.

---

## PASSO 1 — Subir o keystore para o GitHub

O keystore foi gerado pelo Bubblewrap na sua máquina.
Você precisa converter o arquivo `android.keystore` para Base64
e salvar como Secret no GitHub.

### No Windows (PowerShell):
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\caminho\para\android.keystore")) | Set-Clipboard
```
*(isso copia o Base64 para a área de transferência)*

### No Mac/Linux:
```bash
base64 -i android.keystore | pbcopy
```

---

## PASSO 2 — Criar os Secrets no GitHub

No repositório GitHub:
**Settings → Secrets and variables → Actions → New repository secret**

Crie estes 4 secrets:

| Nome                | Valor                                    |
|---------------------|------------------------------------------|
| `KEYSTORE_BASE64`   | Cole o Base64 copiado no passo anterior  |
| `KEY_ALIAS`         | `android` (ou o alias que você usou)     |
| `KEYSTORE_PASSWORD` | Senha que você digitou no Bubblewrap     |
| `KEY_PASSWORD`      | Senha da chave (normalmente a mesma)     |

---

## PASSO 3 — Rodar o build

- Faça push de qualquer coisa para a branch `main`, OU
- Vá em **Actions → Build Lendo Bem APK → Run workflow**

---

## PASSO 4 — Baixar o APK

Após ~5 minutos:
**Actions → último build → Artifacts → lendobem-apk**

Baixa o ZIP que contém:
- `app-release-signed.apk` — instala direto no Android para testar
- `app-release-bundle.aab` — envia para a Play Store

---

## PASSO 5 — Criar o assetlinks.json no servidor

No log do build, step **"Show SHA-256 fingerprint"**, copie o valor.

Crie o arquivo em:
`https://api.dicasbrasil.com.br/.well-known/assetlinks.json`

```json
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.dicasbrasil.lendobem",
    "sha256_cert_fingerprints": ["COLE_O_SHA256_AQUI"]
  }
}]
```

Sem esse arquivo o app abre com a barra do Chrome visível.
Com ele, abre sem barra — parece app nativo.

---

## Rebuild

Para gerar um novo APK basta ir em:
**Actions → Build Lendo Bem APK → Run workflow → Run**
