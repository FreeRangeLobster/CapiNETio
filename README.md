# CapiNETio
# CapiNETio

##To Do List:
- Update all models
- OK Add a new power connector 2 poles
- OK Add a switch to allow power from the bus
- OK Replace the 2 bus strips for one single one
- OK create the layout and angle the in/Out Connectors
- Check type of components and pcb constrains


git push -u origin main

Microcontroller output max per pin 

IO
Page73 of datasheet
Output current sunk by any I/O and control pin 25
Output current sourced by any I/Os and control pin - 25

LED current consumption
20ma

***LED indicators
Typical LED (553-0122F – red indicator)
Approximate specs (common for this part family):
• Forward voltage Vf ≈ 1.9–2.1 V
• Good brightness at 5–10 mA (no need to push 20 mA on STM32 pins)
STM32 GPIO max per pin is usually ~20 mA absolute max, but 5–8 mA is best practice.
Resistor calculation (3.3 V pin)
At 10 mA:
R = (3.3 − 2.0) / 0.01 = 130 Ω
At 5 mA (recommended for MCU safety):
R = (3.3 − 2.0) / 0.005 = 260 Ω
✅ Best real-world values
Current	Resistor
~10 mA	150 Ω
~6 mA	220 Ω
~4 mA	330 Ω
👉 I’d use 220 Ω — bright enough and easy on the STM32 pin
👉 330 Ω if you want conservative current
Wiring
PA4 → resistor → LED → GND
(or LED to 3.3 V and pin sinks current — both fine)
⚠️ Important STM32 tip
Try to keep:
≤ 8 mA per pin
≤ 100 mA total for the whole GPIO port
If you want, I can:
✔️ Check LED datasheet current rating
✔️ Confirm STM32 pin limits exactly
✔️ Show pull-up vs pull-down drive best practice

***Dip Switch
10 kΩ pull-up resistors (standard value)
Per input:
3.3V ── 10kΩ ── GPIO pin ── switch ── GND
You’ll need 4 resistors (one per input).





***Inputs 5 to 24V. Pin3 inputs needs to be 0V common :

1.5 kΩ, 0.5 W resistor
5.1 V Zener diode (0.25 W or higher)
LEDs in series (indicator + opto)

indicator LED (551-0207-004F) in series with the TLP290 LED and a requirement that it must reliably turn ON already at 5 V, we should bias this a bit stronger than the “borderline” case and still keep it safe at 24 V.

Let’s design it like a proper industrial input.

🔧 Known drops (worst-case friendly)

Typical at a few mA:

• Indicator LED ≈ 2.0 V
• TLP290 LED ≈ 1.2 V

👉 Total ≈ 3.2 V

🎯 Target currents

To guarantee turn-on across temperature & aging:

✅ Minimum at 5 V → ≈2 mA
✅ Normal at 24 V → 6–10 mA

This keeps CTR healthy and LEDs safe.

🧩 Use Zener clamp (still the right approach)

Clamp voltage: 5.1 V

So effective resistor voltage at clamp:

5.1 − 3.2 = 1.9 V

🧮 Pick resistor for strong low-end + safe high-end

Let’s choose R = 1.5 kΩ (this is the sweet spot now).

📊 Check operation
✅ At 5 V input (no heavy clamp yet):
I ≈ (5 − 3.2) / 1.5k ≈ 1.2 mA


Plus clamp assistance as it approaches 5.1 V → roughly 1.5–2 mA effective

Enough to switch TLP290 reliably.

✅ At 24 V input (clamped):
I ≈ (24 − 5.1) / 1.5k ≈ 12.6 mA


Still within safe continuous LED current.

🔥 Power check (important)

Resistor:

P ≈ I²R ≈ (0.0126)² × 1500 ≈ 0.24 W


👉 Use ½ W resistor (recommended for longevity)

Zener:

P ≈ 5.1 × 0.0126 ≈ 64 mW


Any 0.25 W zener is fine.

✅ Final rugged values (guaranteed 5–24 V)
Input side:

🟢 1.5 kΩ, 0.5 W resistor
🟢 5.1 V Zener diode (0.25 W or higher)
🟢 LEDs in series (indicator + opto)

📌 This gives you:

✔ Turns ON at 5 V every time
✔ Safe at 24 V continuously
✔ Good noise margin
✔ Long LED life

If you’d like I can:

✔️ Add reverse polarity protection diode
✔️ Add surge suppressor (TVS)
✔️ Show exact schematic symbol placement

Just say — this is shaping up like a professional PLC input channel 💪

what zener smd zener diosde to use

Here are some **good SMD Zener diode options you can use as your 5.1 V clamp on the input side of your optocoupler circuit (to clamp voltage around ~5.1 V for a 1.5 kΩ series resistor):

Nexperia BZX884‑C5V1 Zener Diode
€0.19
•
JAK Electronics + others
onsemi MMBZ5231ELT1G Zener Diode
€0.26
•
DigiKey Italy
DiodesZetex 5.1V Zener (SMD)
€0.26
•
DigiKey Italy
Vishay BZM55C5V1TR MicroMELF Zener
€0.17
•
DigiKey Italy + others
ROHM PDZVTFTR5.1B Zener Diode
€0.39
•
Mouser Italia
ROHM KDZVTFTR5.1B Zener Diode
€0.54
•
Mouser Italia
onsemi 1SMB5918BT3G 5.1 V Zener
€0.64
•
Mouser Italia
Vishay AEC‑Q101 5.1 V Zener (automotive)
€15.80
•
RS Italy
🟡 Best general-purpose low-power (0.25–0.35 W)

These are ideal for clamping in your optocoupler input. Typical power rating ~250–350 mW which is enough for ~5–10 mA clamp:

Nexperia BZX884‑C5V1 Zener Diode – SOT-23, very common, cheap, 5.1 V clamp.

onsemi MMBZ5231ELT1G Zener Diode – another SOT-23 5.1 V option at low cost.

DiodesZetex 5.1V Zener (SMD) – generic 5.1 V Zener in compact SMD package.

Vishay BZM55C5V1TR MicroMELF Zener – MicroMELF style, tiny but robust.

ROHM PDZVTFTR5.1B Zener Diode – well-known brand part.

🟢 Higher power / automotive or robust

If you want more clamp headroom or automotive spec:

ROHM KDZVTFTR5.1B Zener Diode – similar to above with slightly different rating.

onsemi 1SMB5918BT3G 5.1 V Zener – much higher power, overkill for this but good for surge protection too.

Vishay AEC‑Q101 5.1 V Zener (automotive) – automotive-grade if you need rugged industrial reliability.

📌 How to choose

✔ For typical PCB input clamping and currents ~5-10 mA: go with BZX884-C5V1 or MMBZ5231ELT1G in SOT-23 — they are small, cheap, and easy to place.
✔ If you want more power handling or are worried about surges, pick a 500 mW part like ROHM or larger packages.



BZX884-C5V1


D1 – SS14 / BAT54 (Schottky)
R1 – 1.5kΩ 0.5W
ZD1 – 5.1V SMD Zener (SOT-23 or SOD-123)
R2 – 10kΩ
LED1 – 551-0207-004F
LED2 – TLP290

outputs relay, the LED calculations:
Device	Vf
551-0207-004F LED	~2.0 V
TLP290 LED	~1.2 V
Total	~3.2 V

STM32 output = 3.3 V

⚠️ T.hat leaves only:

3.3 − 3.2 = 0.1 V
not a good idea to put them in series. so need to be in parallel
GPIO → 390Ω → TLP290 LED → GND
GPIO → 1kΩ  → indicator LED → GND







Songs:

nati botero te quiero mucho, ponka el problemon, no todo puede llamarse amor jorge celedon, todo de cabeza, la indiferencia de silvestre dangon, labios compartidos, amor de verano eddie, pump it black eyed peas, sexy back justin, i wann love you akon, crazy knarrls barkley, gwen stefany sh=weetes, rehab amy winehouse, bad date daniel, welcome to the black parade, supermassive blackhole muse, snow red hot chilly pepers, 