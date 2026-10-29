# Gyro Ball RPM

A single-file, browser-based tool that listens to a gyro ball wrist exerciser through the microphone and estimates its rotation speed (typically 3,000–10,000 rpm) from the sound spectrum. No build step, no dependencies, no data leaves the device.

## Usage

Open `index.html` in a browser, or serve it over HTTPS (phones only allow microphone access on secure pages):

```sh
python3 -m http.server   # then open http://localhost:8000
```

**Live app:** [https://berkenbu.github.io/gyroball-rpm/](https://berkenbu.github.io/gyroball-rpm/) (hosted on GitHub Pages, so the microphone works on phones).

Hold the phone a few centimetres from the ball in a quiet room and press **Start microphone**. **Simulated test** runs a synthetic sweep so you can check the display without a ball.

## How it works

- Rotation speed in Hz is rpm / 60, so 3,000–10,000 rpm is 50–167 Hz.
- The microphone signal goes through a 32768-point FFT (about 1.5 Hz bins at 48 kHz). Echo cancellation, noise suppression and auto gain are disabled.
- Spectrum peaks are measured against a moving-average noise floor, so the result doesn't depend on mic gain.
- **Auto mode** scores every candidate speed by how well it explains peaks at 1×, 2×, … 8× (weak fundamentals are common because phone mics roll off below ~100 Hz). The best candidate is refined with parabolic peak interpolation across its matched harmonics, then median-smoothed.
- **Fixed modes** treat the strongest peak in range as the 1×–4× harmonic. Use them if Auto reads half or double the true speed.

## Limitations

- Accuracy is limited by the FFT window (about 0.7 s), so fast speed changes are smoothed.
- Loud background tones (fans, mains hum at 50/60 Hz and its harmonics) can confuse detection; narrow the min/max range if needed.

## License

MIT
