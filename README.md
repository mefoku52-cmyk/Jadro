# 🤖 Neural Link Omega: A Hybrid AI System Governor for Termux (Android)

## Project Overview

**Neural Link Omega** is a highly specialized, hybrid system for dynamic CPU resource management, designed specifically to operate within the constraints of the Termux environment on Android devices.

This project is a proof-of-concept demonstrating a custom, user-space "AI Governor" that bypasses standard Linux/Android kernel schedulers to implement bespoke performance and thermal throttling logic.

**The core innovation is the Inter-Process Communication (IPC) architecture:** a high-level BASH supervisor communicates with a performance-optimized Rust native core via a Named Pipe (FIFO), allowing for ultra-low-latency control over intensive background tasks.

## 🌟 Key Features

* **Hybrid Architecture:** Combines the robust process management capabilities of **BASH** (for Watchdog and process signaling) with the speed and safety of **Rust** (for AI computation).
* **IPC Communication:** Utilizes a **Named Pipe (FIFO)** for lightning-fast, unidirectional data exchange (CPU limit values) between the BASH supervisor and the Rust core.
* **Dynamic Throttling Logic (AI Governor):** The Rust core implements a custom algorithm (`compute_limit`) that dynamically adjusts CPU usage limits based on simulated thermal data, effectively acting as a user-space thermal manager.
* **Termux/Android Resilience:** Robust handling of environment-specific challenges:
    * **Permission Denials:** PID and Pipe files are safely located within the `$HOME/.neural_link_omega` directory.
    * **CPULIMIT Fallback:** Automatically detects if the native `cpulimit` utility fails (common on some Android kernels) and seamlessly falls back to the **`renice`** command for process priority management.
    * **Daemonization:** Uses `tmux` and `nohup` for stable, persistent operation in the background, surviving terminal closures.

## 🛠️ Architecture and Data Flow

1.  **BASH Supervisor (`omega_core.sh`):**
    * Starts a synthetic intensive task (`Target PID`).
    * Starts the Rust Core (`Core Engine PID`).
    * Continuously reads the calculated CPU limit from the Named Pipe.
    * Applies the limit using `cpulimit` or `renice` fallback.
    * Runs the **Watchdog** to monitor and automatically restart both the Target PID and the Rust Core if they crash.
2.  **Rust Native Core (`core_engine`):**
    * Reads simulated thermal data from a file.
    * Runs the proprietary `compute_limit` algorithm (AI logic).
    * Writes the calculated limit (e.g., `95`, `50`) to the Named Pipe.

---

## 🚀 Installation & Usage (Termux)

### Prerequisites

* Termux with internet access.
* The `rust` and `cpulimit` packages installed (`pkg install rust cpulimit`).

### Full Installation & Deployment

Execute the final monolithic script (which includes setup, compilation, and launch) from your Termux terminal:

```bash
# PASTE THE FINAL MONOLITHIC SCRIPT HERE
# NOTE: The final script is too long to include here, but should be added 
# to a separate file (e.g., install.sh) or directly into the README.
# For simplicity, assume the core script is already created/compiled.

# --- To launch the system in the background using tmux ---
pkg install -y tmux
tmux new-session -d -s neural 
tmux send-keys -t neural:0 "$HOME/omega_core.sh" C-m

echo "System is running. Attach to the session to view logs."
MIT Licencia

Copyright (c) 2024 Radoslav Čornanič, Slovensko, Ulič 63, PSČ 06767

Týmto sa udeľuje bezplatné povolenie každej osobe, ktorá získa kópiu tohto softvéru a súvisiacich súborov dokumentácie (ďalej len "Softvér"), zaobchádzať so Softvérom bez obmedzenia, vrátane, ale nie výlučne, práv na používanie, kopírovanie, úpravu, zlúčenie, publikovanie, distribúciu, sublicencovanie a/alebo predaj kópií Softvéru a povolenie osobám, ktorým je Softvér poskytnutý, aby tak urobili, za nasledujúcich podmienok:

Vyššie uvedené oznámenie o autorských právach a toto oznámenie o povolení musia byť zahrnuté vo všetkých kópiách alebo podstatných častiach Softvéru.

SOFTVÉR SA POSKYTUJE "TAK, AKO JE", BEZ ZÁRUKY AKÉHOKOĽVEK DRUHU, VÝSLOVNEJ ALEBO PREDPOKLADANEJ, VRÁTANE, ALE NIE VÝLUČNE, ZÁRUK PREDAJNOSTI, VHODNOSTI NA KONKRÉTNY ÚČEL A NEPORUŠENIA PRÁV. AUTORI ANI DRŽITELIA AUTORSKÝCH PRÁV NEBUDÚ V ŽIADNOM PRÍPADE ZODPOVEDNÍ ZA ŽIADNU POHĽADÁVKU, ŠKODU ANI INÚ ZODPOVEDNOSŤ, ČI UŽ V RÁMCI ZMLUVY, DELIKTU ALEBO INAK, VYPLÝVAJÚCU ZO SOFTVÉRU, Z JEHO POUŽÍVANIA ALEBO INÉHO ZAOBCHÁDZANIA S NÍM.
