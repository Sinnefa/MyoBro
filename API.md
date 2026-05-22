<div class="container">

<div>

# MyoBro API

A bio-computational engine for predictive human metabolism.

</div>

<div id="overview" class="section">

## What This API Does

The MyoBro API is not a simple calorie counter. It is a highly advanced,
**proprietary mathematical engine** designed to calculate complex human
physiological states in the continuous time domain. By feeding it a
chronological sequence of meals and workout events, the engine
discretizes bi-compartmental pharmacokinetic models and hormonal
transients to predict exactly what is happening inside the human body
over a 24-hour cycle. The average response time was approximately 135 ms
with a maximum below 750 ms.
<a href="https://www.myobro.com/api/documentation/k6_report.html"
target="_blank" style="text-decoration:inherit;color:inherit">K6 Test
Results</a>

<div class="grid-list">

<div class="feature-card">

#### mTORC1 & MPS Modeling

Calculates exact Muscle Protein Synthesis (MPS) curves, evaluating
Leucine triggers, intracellular receptor refractory states, and
mechanical tension bypasses (PI3K pathway).

</div>

<div class="feature-card">

#### Dynamic Gastric Emptying

Applies customized delay coefficients (Tau dilation). It automatically
slows down digestion curves based on the precise grams of fats and
fibers ingested, ensuring accurate macro-nutrient delivery times.

</div>

<div class="feature-card">

#### Hormonal Surges & Hypoglycemia

Predicts post-prandial insulin spikes. By analyzing the negative
derivative of the blood-glucose clearance curve, the API accurately
predicts the exact time a user will experience reactive hypoglycemia
(physical hunger).

</div>

<div class="feature-card">

#### Optimal Workout Windows

Cross- references anabolic drive with low-insulin (lipolytic) states to
calculate the exact chronological windows where training will yield
maximum physiological adaptation and fat oxidation.

</div>

</div>

</div>

<div id="authentication" class="section">

## Authentication & Billing

The MyoBro API utilizes a strict, highly concurrent **token consumption
architecture**. One successful mathematical computation equals one
credit deduction.

<div class="callout warning">

**Acquiring Access:** To obtain a valid `username` and recharge your
`token` balance, you must directly contact our infrastructure team at:
<myobro@myobro.com>  
**English:** Access to Dashboard, API and its proprietary algorithm is
strictly governed by a non-exclusive software usage license, in
accordance with the Italian Copyright Law (Law no. 633 of April 22,
1941, as amended). This is not an automated subscription service.
Licenses are negotiated privately, have a defined time validity (e.g.,
free, 3, 6, or 12 months), and include a set number of usage credits
(tokens). To discuss your computing needs and request a license, please
contact me directly.  
  
**Italiano:** L'accesso alal Dashboard, alle API e al relativo algoritmo
proprietario è regolamentato esclusivamente tramite cessione di licenza
d'uso non esclusiva, ai sensi della Legge sul Diritto d'Autore (Legge 22
aprile 1941, n. 633 e successive modifiche). Non si tratta di un
servizio in abbonamento automatizzato. Le licenze vengono concordate
privatamente, hanno una validità temporale definita (es. gratis, 3, 6 o
12 mesi) e includono un numero prestabilito di crediti (token) per
l'utilizzo. Per discutere le tue necessità di calcolo e richiedere una
licenza, contattami direttamente.

</div>

|                        |                                                                       |
|------------------------|-----------------------------------------------------------------------|
| HTTP Status            | Description                                                           |
| `401 Unauthorized`     | Missing payload credentials or incorrect Username/Token combination.  |
| `402 Payment Required` | Token valid, but credits are exhausted (Balance: 0). Request aborted. |
| `500 Internal Error`   | Database concurrency lock or internal engine failure.                 |

</div>

<div id="endpoints" class="section">

## Endpoint Interaction

<div class="endpoint">

<span class="method">POST</span> https://www.myobro.com/api/v1/

</div>

### 1. The Request Payload (Input)

The payload must be a valid JSON object containing your credentials, the
physiological configuration (optional), and the array of chronological
nutritional events.

    {
      "credentials": {
        "username": "your_username",
        "token": "your_secure_token"
      },
      "config": {
        // Optional overrides. Omit to use validated baseline algorithms.
      },
      "meals": [
        {
          "timeDec": 7.5,
          "p": 30,
          "c": 45,
          "f": 12,
          "fib": 4,
          "l": 3.1,
          "isPreWorkout": false,
          "isPostWorkout": false
        }
      ]
    }

#### Meals Array Object Parameters

|                 |         |                                                                                                                                           |
|-----------------|---------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Parameter       | Type    | Description                                                                                                                               |
| `timeDec`       | Float   | **Required.** The time of the event in 24h decimal format (e.g., `7.5` = 07:30 AM).                                                       |
| `p` (or `prot`) | Float   | Total protein amount in grams.                                                                                                            |
| `c`             | Float   | Total carbohydrate amount in grams. Contributes heavily to the insulin surge magnitude.                                                   |
| `f`             | Float   | Total fats in grams. Mechanically blunts gastric transit, delaying the kinetic curve peak.                                                |
| `fib`           | Float   | Total dietary fiber in grams. Synergizes with fats to stretch the mathematical curve (Tau dilation).                                      |
| `l`             | Float   | Total Leucine in grams. **Critical:** This is the bio-chemical trigger evaluated against the MPS threshold to initiate an anabolic state. |
| `isPreWorkout`  | Boolean | If true, simulates catecholamine-induced vasoconstriction, mathematically slowing digestion.                                              |
| `isPostWorkout` | Boolean | If true, applies mechanical tension bypass, shortening the receptor refractory period (Muscle Full Effect) to allow earlier re-feeding.   |

#### Configuration Constants (Advanced)

<div class="callout warning">

**Notice on Defaults:** The core engine utilizes default constants that
have been deeply calibrated and scientifically validated to mirror real
human in-vivo responses. **Do not override these values** unless you are
conducting specialized kinetic research or simulating extreme metabolic
outliers.

</div>

<div id="table-container">

<table class="constants-table" data-border="1" data-cellpadding="8"
style="border-collapse: collapse; width: 100%; text-align: left;">
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th>Constant</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="white-space: nowrap"><strong>BAT_MPS_MAX_LIMIT</strong><br />
<span class="small">Hypertrophic Potential (Max Limit)</span></td>
<td>Synthesis ceiling: ~2.5 (sedentary), 4.0 (trained), &gt;5.0 (elite).
The genetic or physiological ceiling for protein synthesis. Higher
limits reflect trained athletes with greater total muscle mass; lower
limits fit sedentary users.</td>
</tr>
<tr class="even">
<td
style="white-space: nowrap"><strong>MPS_LEUCINE_THRESHOLD</strong><br />
<span class="small">Anabolic Resistance (Activation Target)</span></td>
<td>Activation threshold: ~2.0g (youth), 2.5g (adult), &gt;3.5g
(elderly/sarcopenic). The minimum leucine dose required to fully
activate mTORC1. Higher values simulate age-related anabolic resistance
or high systemic inflammation.</td>
</tr>
<tr class="odd">
<td style="white-space: nowrap"><strong>MPS_IDEAL_CADENCE</strong><br />
<span class="small">Muscle Full Effect (Refractory Period)</span></td>
<td>Resensitization time: ~3.0h (elite/post-workout), 4.0h (standard),
~5.0h+ (sedentary). Determines how long muscle cells remain "deaf" to
amino acids after a meal. Resistance training shortens this window,
requiring more frequent feeding.</td>
</tr>
<tr class="even">
<td style="white-space: nowrap"><strong>INS_T_BASE_PEAK</strong><br />
<span class="small">Insulin Sensitivity (Peak Speed)</span></td>
<td>Hormonal reflex speed: ~0.6h (highly sensitive), 0.8h (normal),
&gt;1.2h (insulin resistant). Represents how quickly the pancreas
releases a Phase-1 insulin surge. Slower times flatten the curve,
prolonging glucose disposal.</td>
</tr>
<tr class="odd">
<td
style="white-space: nowrap"><strong>HUNGER_THRESHOLD_DELTA</strong><br />
<span class="small">Hunger Time: Metabolic Flexibility (Crash
Tolerance)</span></td>
<td>Hypoglycemic trigger: ~2.0 (sugar dependent), 4.0 (normal), &gt;6.0
(fat-adapted). The steepness of the insulin crash required to trigger a
physiological panic response. Fat-adapted athletes tolerate steep drops
without experiencing cravings.</td>
</tr>
<tr class="even">
<td
style="white-space: nowrap"><strong>HUNGER_DELAY_COLLAPSE</strong><br />
<span class="small">Hunger Time: Gut-Brain Axis (Ghrelin
Lag)</span></td>
<td>Neurological lag: ~0.8h (fast metabolism), 1.1h (standard), &gt;1.5h
(delayed/disrupted). The time gap between the internal metabolic crash
and the actual release of ghrelin, translating into perceived physical
hunger.</td>
</tr>
</tbody>
</table>

</div>

------------------------------------------------------------------------

### 2. The Response Payload (Output)

The API engine returns a highly detailed JSON object containing
calculated data points ready to be plotted on UI graphs, alongside
critical physiological insights.

    {
      "diagnostics": {
        "accuracy_auc_percent": 99.85,
        "auc_ideal_mass": 120.4,
        "auc_real_mass": 120.2
      },
      "mps": {
        "idealData": [{ "x": 7.5, "y": 0.0 }, ...],
        "realCurveData": [{ "x": 7.5, "y": 0.0 }, ...],
        "realPointsData": [{ "x": 7.5, "y": 0, "note": "ok" }],
        "startX": 6,
        "theoretical_next_mps_peak": "11:30"
      },
      "insulin": {
        "idealCurveData": [...],
        "realCurveData": [...],
        "peaksData": [{ "x": 8.2, "y": 45.6, "mealIndex": 0 }],
        "hungerTime": "10:15"
      },
      "workout": {
        "status": "success",
        "workout_windows": [
          {
            "start_dec": 9.5,
            "start": "09:30",
            "end_dec": 11.5,
            "end": "11:30"
          }
        ],
        "message": "Workout windows calculated"
      }
    }

#### Response Object Definitions

**`diagnostics` (System Integrity)**

|                                    |                                                                                                                                                                                                                              |
|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Field                              | Description                                                                                                                                                                                                                  |
| `accuracy_auc_percent`             | Validates the First Law of Thermodynamics within the engine. Measures the integral Area Under the Curve (AUC) to ensure temporal dilation (from fats/fibers) did not artificially "destroy" or "create" macro-nutrient mass. |
| `auc_ideal_mass` / `auc_real_mass` | The absolute integral values of the theoretical vs. real discretized curves.                                                                                                                                                 |

**`mps` (Muscle Protein Synthesis Kinetics)**

|                             |                                                                                                                                                                                       |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Field                       | Description                                                                                                                                                                           |
| `idealData`                 | Array of \[X,Y\] coordinates plotting the theoretical perfect biological response.                                                                                                    |
| `realCurveData`             | Array of \[X,Y\] coordinates plotting the \*actual\* user response, heavily modified by meal composition, delays, and sub-optimal leucine triggering.                                 |
| `realPointsData`            | Marks specific X-axis injection events. The `"note": "ok"` flag indicates a successful activation of the anabolic state.                                                              |
| `startX`                    | The starting coordinate on the time axis.                                                                                                                                             |
| `theoretical_next_mps_peak` | A formatted string (e.g., "15:45") predicting the exact ideal time the user must consume their next protein meal to bypass the refractory period and sustain maximal daily anabolism. |

**`insulin` (Hormonal & Glycemic Dynamics)**

|                 |                                                                                                                                                                                                                         |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Field           | Description                                                                                                                                                                                                             |
| `realCurveData` | Array of \[X,Y\] coordinates tracking blood sugar clearance and rapid biphasic beta-cell insulin secretion.                                                                                                             |
| `peaksData`     | Identifies local maxima. Contains the X/Y coordinates of every individual insulin surge mapped back to its specific `mealIndex`.                                                                                        |
| `hungerTime`    | A formatted string (e.g., "11:20"). By calculating the steepest negative derivative of the insulin curve, the model mathematically predicts the exact time systemic Ghrelin release will cause intense physical hunger. |

**`workout` (Physiological Windowing)**

|                   |                                                                                                                                                                                                                                                                                                                                                |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Field             | Description                                                                                                                                                                                                                                                                                                                                    |
| `workout_windows` | An array of objects dictating optimal training times. A window opens when the insulin transient enters a safe declining phase (preventing reactive hypoglycemia and allowing fat oxidation) and closes when the kinetic drive of macro-nutrients is fully exhausted. Provides both decimal (`start_dec`) and human-readable (`start`) formats. |

</div>

</div>
