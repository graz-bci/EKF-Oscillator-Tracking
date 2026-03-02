# EKF-Oscillator-Tracking
🌀 EKF Oscillator Tracking (EKFOT) with GA Hyperparameter Optimization

This repository provides a MATLAB framework for tracking time-varying narrowband oscillations (frequency + damping + amplitude proxy) using an Extended Kalman Filter (EKF) applied to a reparametrized AR(2) resonator model.

It includes:

- an EKF-based oscillator tracker (SIM_EKFOT, run_EKFOT)

- a cost function for tuning filter hyperparameters (EKFOT)

- a Genetic Algorithm (GA) wrapper for optimization across multiple signals/trials (GA_EKFOT)

- optional GA + fmincon hybrid refinement for stable parameter selection


## 📁 Structure

```text
├── code
│   ├── EKFOT.m           # EKF oscillator tracking cost for optimization
│   ├── GA_EKFOT.m        # GA wrapper: objective over trials/channels + GA call
│   ├── run_EKFOT.m       # Runs EKFOT tracking and returns amplitude/frequency/uncertainty
│   ├── SIM_EKFOT.m       # EKF oscillator tracking: returns full state trajectories
│   └── (wrapToPiLocal.m) # Local helper for wrapping phase (needed)
├── README.md
└── LICENSE
