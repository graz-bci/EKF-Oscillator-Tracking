# 🌀 EKF Oscillator Tracking (EKFOT)

MATLAB implementation of an **Extended Kalman Filter (EKF)** for tracking  
time-varying **frequency**, **damping**, and **amplitude** of narrowband oscillations  
using a reparametrized AR(2) resonator model.

The repository includes:

- ✅ EKF-based oscillator tracking  
- ✅ Genetic Algorithm (GA) hyperparameter optimization  
- ✅ Multi-channel / multi-trial support  
- ✅ Innovation-based cost function  

---

## 📁 Repository Structure

```
/ [root]
├── code
│   ├── EKFOT.m          % EKF oscillator tracking cost (used by GA)
│   ├── GA_EKFOT.m       % GA objective across channels/trials
│   ├── SIM_EKFOT.m      % EKF tracking returning full state trajectories
│   ├── run_EKFOT.m      % Wrapper to run tracking on multichannel data
│   └── wrapToPiLocal.m  % Helper for frequency wrapping
├── README.md
└── LICENSE
```

---

## 🧠 Model Description

The observed signal is modeled as a noisy AR(2) resonator:

State vector:

```
z(k) = [ x(k), x(k-1), omega(k), alpha(k) ]
```

Measurement equation:

```
y(k) = x(k) + v(k)
```

AR(2) coefficients are parameterized as:

```
a1 =  2*exp(-alpha*Ts)*cos(omega*Ts)
a2 = -exp(-2*alpha*Ts)
```

where:

- `omega` = instantaneous angular frequency (rad/s)  
- `alpha` = damping coefficient (1/s)  
- `Ts` = sampling period  

An **Extended Kalman Filter** estimates:

- Oscillator states `x(k), x(k-1)`
- Instantaneous frequency `omega(k)`
- Damping `alpha(k)`
- State covariance evolution

---

## ⚙️ Hyperparameter Optimization (GA)

The EKF parameters are optimized using a **Genetic Algorithm**.

Optimized parameter vector:

```
X = [R2_ct, Q_ct, omega0, alpha0, P0scale]
```

Where:

- `R2_ct`  → continuous-time measurement noise intensity  
- `Q_ct`   → continuous-time process noise intensity  
- `omega0` → initial angular frequency (rad/s)  
- `alpha0` → initial damping  
- `P0scale` → initial covariance scaling  

Continuous-to-discrete scaling:

```
R2 = R2_ct * Ts
R1 = Q_ct  * Ts * eye(4)
```

---

## 🎯 Cost Function

The optimization minimizes normalized innovation energy:

```
J = norm(innovation(ignore:end)) / norm(signal(ignore:end))
```

Where:
- `innovation` = EKF prediction error
- `ignore` = number of initial samples discarded (transient removal)

---

## 🚀 Example Usage

### 1️⃣ Optimize EKF parameters

```matlab
Fs     = 100;      % sampling rate
ignore = 200;      % discard transient samples

[lamm, err] = optimize_EKFOT(ytr, ignore, Fs);
```

Expected shape:

```
ytr : [M x T x nTrials]
```

---

### 2️⃣ Run EKF oscillator tracking

```matlab
polf = run_EKFOT(sig_in, lamm, ignore, Fs);

M = size(sig_in,1);

A   = polf(1:M, :);        % Amplitude proxy
f   = polf(M+1:2*M, :);    % Frequency (Hz)
ptr = polf(2*M+1:3*M, :);  % Uncertainty proxy
```

---

## 📊 Outputs

For each channel:

### 🔹 Amplitude estimate

```
A(k) = sqrt(x1(k)^2 + x2(k)^2)
```

### 🔹 Frequency estimate

```
f(k) = abs(omega(k)) / (2*pi)
```

### 🔹 Uncertainty proxy

```
ptr(k) = norm(P(k),'fro')
```

---

## 🧪 Recommended Workflow

1. Bandpass filter signal around rhythm of interest  
2. Optimize EKF hyperparameters via GA  
3. Run EKF tracking  
4. Analyze frequency drift, amplitude modulation, and uncertainty  

---

## ⚠️ Notes

- Designed for **narrowband oscillatory signals**
- Works best after preprocessing (bandpass filtering)
- `ignore` should remove filter + EKF initialization transients
- Requires `wrapToPiLocal.m` (or replace with MATLAB `wrapToPi`)

---

## 📚 Citation

To be announced

---

## 📝 License

Specify your preferred license (MIT, GPL, etc.) in `LICENSE`.
