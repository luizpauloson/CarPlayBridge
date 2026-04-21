# CarPlay Bridge — Instruções de Build e Uso

## O que este APK faz

1. **Detecta qualquer dongle USB** conectado na multimídia, independente do VID/PID
2. **Mostra informações diagnósticas** completas do dongle (fabricante, VID, PID, serial)
3. **Solicita permissão USB automaticamente** (o problema mais comum com o AutoKit)
4. **Tenta inicializar o protocolo CarPlay** do Carlinkit 4.0 via USB control transfers
5. **Roda em background** como serviço foreground para manter a conexão
6. **Inicia automaticamente** no boot se o dongle já estiver conectado

---

## Como compilar (5 minutos)

### Pré-requisitos
- **Android Studio** instalado no PC (gratuito: https://developer.android.com/studio)
- Java JDK 17+ (o Android Studio já inclui)

### Passos

1. Abra o Android Studio
2. **File → Open** → selecione a pasta `CarPlayBridge`
3. Aguarde o Gradle sincronizar (1-2 minutos na primeira vez)
4. Clique em **Build → Build Bundle(s)/APK(s) → Build APK(s)**
5. O APK estará em: `app/build/outputs/apk/debug/app-debug.apk`

### Instalar na multimídia

**Opção A — Pen drive:**
1. Copie o `app-debug.apk` para um pen drive
2. Conecte o pen drive na multimídia
3. Use o gerenciador de arquivos do Android para instalar

**Opção B — ADB (mais rápido):**
```
adb connect <IP_DA_MULTIMIDIA>:5555
adb install app-debug.apk
```
Para descobrir o IP: na multimídia, vá em Configurações → WiFi → toque na rede conectada

---

## Como usar

1. Instale o APK na multimídia
2. Abra o app **CarPlay Bridge**
3. Conecte o Carlinkit 4.0 na porta USB
4. O app vai mostrar o log com VID/PID do dongle
5. **Copie o VID e PID mostrados** (ex: VID=0x1D5C PID=0x7104)
6. Se o CarPlay não conectar automaticamente, mande o log para diagnóstico

---

## Interpretando o log

| Mensagem | Significado |
|---|---|
| `✅ RECONHECIDO como Carlinkit!` | VID/PID está na lista conhecida, tentando inicializar |
| `ℹ️ VID/PID não está na lista` | Dongle usa VID/PID diferente — mas ainda vai tentar |
| `✅ Permissão USB concedida!` | Android deu acesso ao dongle |
| `❌ Permissão USB negada` | Toque em "Escanear USB" e aceite o popup de permissão |
| `🚀 Iniciando CarPlay Bridge` | Serviço de conexão iniciado |
| `✅ CarPlay ativo!` | Inicialização concluída, conecte o iPhone |

---

## Próximos passos (Fase 2)

Com o VID/PID em mãos, podemos:
- Adicionar o par exato ao filtro USB para detecção instantânea
- Ajustar a sequência de inicialização para o firmware específico do seu dongle
- Implementar conexão WiFi direta com o iPhone (sem cabo)

---

## Estrutura do projeto

```
CarPlayBridge/
├── app/src/main/
│   ├── AndroidManifest.xml       # Permissões USB, filtros, serviço
│   ├── java/com/carplaybridge/
│   │   ├── MainActivity.java     # UI diagnóstico + detecção USB
│   │   ├── CarPlayService.java   # Serviço background + protocolo Carlinkit
│   │   └── BootReceiver.java     # Auto-start no boot
│   └── res/
│       ├── layout/activity_main.xml
│       └── xml/usb_device_filter.xml  # Aceita qualquer USB
└── README.md
```
