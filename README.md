# CEC 2026 — Online EV Charging Benchmark Instances

Benchmark instances for the **online electric-vehicle (EV) charging scheduling** problem, accompanying the paper submitted to IEEE CEC 2026.

> Source code will be added to this repository upon paper acceptance.

## Repository Structure

```
instances_long_horizon_booking/    # Long-horizon (LH) booking scenario
instances_mixed_booking/           # Mixed (MD) booking scenario
instances_short_notice_booking/    # Short-notice (SN) booking scenario
```

Each folder contains:

| File pattern | Description |
|---|---|
| `chargers_n{N}.csv` | Charger configurations for *N* ∈ {10, 20, 40, 50, 100, 200} |
| `online_n{N}_{scenario}_rep{R}_demands.csv` | Demand instances — 20 replications per size |

## Data Format

### Chargers (`chargers_n{N}.csv`)

| Column | Type | Description |
|---|---|---|
| `charger_id` | int | Unique charger identifier |
| `power_kw` | float | Rated charging power in kW |

### Demands (`*_demands.csv`)

| Column | Type | Description |
|---|---|---|
| `vehicle_id` | int | Unique vehicle identifier |
| `submission_time` | float | Time the charging request is submitted (hours) |
| `arrival_time` | float | Vehicle arrival time at the station (hours) |
| `departure_time` | float | Vehicle departure deadline (hours) |
| `required_energy` | float | Energy demand in kWh |

## Instance Generation

Instances are synthetic benchmarks adapted from [Zaidi et al. (2022)](https://doi.org/10.1016/j.apenergy.2022.119440) and extended to model advance booking and online decision-making.

Each instance consists of *n* charging demands generated as follows:

- **Arrival times**: $r_j \sim \mathcal{U}(0,\; 0.2n)$ (hours).
- **Energy requirements**: $e_j \sim \mathcal{U}(5.5,\; 66)$ (kWh).
- **Reference charging time**: $p_j^{(11)} = e_j / 11$ (time on a reference 11 kW charger).
- **Departure times**: $d_j = r_j + (1 + \alpha_j)\, p_j^{(11)}$, where $\alpha_j \ge 0$ is a flexibility factor.

### Flexibility Factor

The admissible range of α_j depends on the reference charging time to reflect reduced flexibility for longer sessions:

| Range of p_j^(11) | Range of α_j |
|---|---|
| [0.5, 1) | [0.1, 1.0] |
| [1, 2) | [0.1, 0.9] |
| [2, 3) | [0.1, 0.8] |
| [3, 4) | [0.1, 0.7] |
| [4, 5) | [0.1, 0.6] |
| [5, 6) | [0.1, 0.5] |

### Station Configuration

Each instance specifies a charging-station configuration with *m* chargers of rated powers *w_i* and a station-level grid capacity *W^g*. A fixed mapping from *n* to (*m*, *W^g*) is used to ensure scalable yet realistic station sizing.

### Booking Modes

To model reservations and walk-in arrivals, each demand is assigned a submission time *st_j ≤ r_j*. Three booking modes are considered:

| Mode | Abbreviation | Submission time |
|---|---|---|
| Long-horizon | LH | $st_j \sim \mathcal{U}(0,\; r_j)$ |
| Short-notice | SN | $st_j \sim \mathcal{U}(\max\{0,\; r_j - 1\},\; r_j)$ |
| Mixed | MD | Each demand follows LH or SN with equal probability |

### Experimental Setup

- Instance sizes: *n* ∈ {10, 20, 40, 50, 100, 200}
- Booking modes: LH, SN, MD
- 20 replications per (*n*, mode) pair

## Loading the Data (C++ Example)

```cpp
#include <fstream>
#include <sstream>
#include <string>
#include <vector>

// Read charger file
std::ifstream charger_file("instances_long_horizon_booking/chargers_n10.csv");
std::string line;
std::getline(charger_file, line); // skip header: charger_id,power_kw
while (std::getline(charger_file, line)) {
    // parse charger_id and power_kw from each CSV line
}

// Read demand file
std::ifstream demand_file("instances_long_horizon_booking/online_n10_long_horizon_booking_rep1_demands.csv");
std::getline(demand_file, line); // skip header
while (std::getline(demand_file, line)) {
    // parse vehicle_id, submission_time, arrival_time, departure_time, required_energy
}
```

## Citation

If you use these instances, please cite:

```
(Citation details will be added after acceptance.)
```

## License

This dataset is provided for academic research purposes.
