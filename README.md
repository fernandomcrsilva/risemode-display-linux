# Rise Mode Display — versão Linux (CachyOS)

Driver Linux para o display de temperatura dos coolers **Rise Mode** que vêm
com o software Windows "CPU TEMP Monitor" (`DeviceDriver.exe`). O software
original é um rebrand do *Ocypus Digital* e o display usa um controlador HID
da Semico: **VID `1a2c` / PID `4984`**.

O protocolo foi extraído por engenharia reversa do executável Windows:

| Byte | Conteúdo |
|------|----------|
| 0    | Report ID `0x07` (HID Feature Report, 65 bytes) |
| 1–3  | Temperatura: centena, dezena, unidade |
| 4    | Flags: bit0 = Fahrenheit, bit4 = modo GPU |
| 5–7  | Uso (%): centena, dezena, unidade |

Há ainda um handshake `[0x07, 0xFD, 0…]` (64 bytes) e o envio se repete a
cada 1 segundo — exatamente como o app original faz.

## Requisitos

Nenhuma dependência além do Python (já incluso no CachyOS). O script fala
direto com `/dev/hidraw` via ioctl — não precisa de hidapi nem pip.

## Instalação rápida (sem pacote)

```bash
chmod +x risemode-display
sudo ./risemode-display list      # confirme que o display aparece (1a2c:4984)
sudo ./risemode-display run       # teste em primeiro plano
sudo ./risemode-display install   # instala em /usr/local/bin + systemd + udev
```

## Instalação via pacman (recomendado no CachyOS)

```bash
makepkg -si    # dentro desta pasta
sudo systemctl enable --now risemode-display
```

## Comandos

```bash
risemode-display list           # lista dispositivos HID e sensores hwmon
risemode-display run            # mostra temp/uso da CPU em °C
risemode-display run -u f       # Fahrenheit
risemode-display run --gpu      # temp/uso da GPU (amdgpu, i915 ou nvidia-smi)
risemode-display run -s coretemp  # força um sensor hwmon específico
risemode-display off            # apaga o display
```

## Modos do segundo dígito (W / %)

O campo `mode` em `/etc/risemode-display.conf` (editável pela GUI) controla o
que aparece ao lado da temperatura:

- `percent` — uso da CPU em % (padrão)
- `watts` — potência da CPU em W (RAPL)
- `system` — potência do sistema em W: CPU (RAPL) + GPU (amdgpu/nvidia-smi).
  É uma estimativa — placa-mãe, RAM e discos não têm sensor de potência.

Na GUI, o toggle W/% escolhe entre % e watts; a caixa "W do sistema inteiro"
faz o W somar CPU + GPU.

## Solução de problemas

- **`lsusb` não mostra `1a2c:4984`** — confira o cabo USB interno do cooler na
  placa-mãe. Se aparecer com outro PID (ex.: `1a2c:434d`, usado pelos Ocypus
  mais antigos), rode com `--pid 0x434d`. O script também aceita
  automaticamente qualquer dispositivo `1a2c` se o PID padrão não for achado.
- **Permissão negada sem sudo** — instale a regra udev
  (`risemode-display install` ou o pacote) e reconecte o dispositivo.
- **Sensor errado** — use `risemode-display list` para ver os sensores e
  escolha com `-s <nome>` (ex.: `k10temp` em AMD, `coretemp` em Intel).
- **Display não muda** — alguns lotes usam o layout antigo do Ocypus
  (dígitos nos bytes 5/6). Se os números não aparecerem, me avise que ajusto
  o script para enviar nos dois formatos.

## Aviso

Projeto da comunidade, sem afiliação com a Rise Mode. Os pacotes enviados são
os mesmos que o software oficial envia; ainda assim, use por sua conta.
