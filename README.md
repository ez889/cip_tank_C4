cip_tank_C4
Study project of CIP: acid tank, modelling from real object.

1. Step 0 – Stop / waiting
  We are in waiting state.
  When the Start button is pressed and the max level sensor `C4_LSH = 0`, the sequence starts and we go to Step 10.

2. Step 10 – Filling (water + acid)
  Valve `C4_V5` opens – water is fed into the tank.
  Dosing pump `C4_DP` starts – acid is pumped into the tank.

When the mixture level in the tank reaches the max level sensor `C4_LSH = 1`, we:
  close water valve `C4_V5`,
  stop dosing pump `C4_DP`,
  and move to **Step 20**.

If the max level `C4_LSH` stays active longer than 10 seconds, this is treated as an overfill:
  drain valve `C4_V2` opens and the mixture is dumped to the sewer,
  appeared 'fb_error'.

3. Step 20 – Work on line L1 + next tank (main process)
With the tank at max level:
  Valve `C4_V1` opens – the mixture from C4 is sent to line L1.
  Pump `L1_P1` starts and pumps the mixture through the line.
  Valve `C4_V6` opens – the mixture is also sent in the direction of the next tank.
  At the start of Step 20 a 5-minute timer `t_circ` is started – this is the process time.

If during this step the min level sensor `C4_LSM = 1` and there is **no flow** on L1 (`L1_protok <= 0`), the block sets `fb_error` and goes back to Step 0.
When the min level is reached (`C4_LSM = 1`) **or** the 5-minute timer has elapsed, we go to Step 30.

4. Step 30 – L1 pump overrun (1 minute)
In this step:
  Pump `L1_P1` keeps running and continues to push the mixture further through the pipes.
  Valve `C4_V6` stays open towards the next tank.
  All other valves on C4 are closed.

At the same time a 1-minute timer `t_PumpDrain` is active.
When this 1-minute timer finishes, all outputs are reset, and the sequence returns to Step 0.

5. Additional protection rule
If the max level sensor `C4_LSH` is active for more than 10 seconds, the system opens the drain valve `C4_V2` and dumps the mixture to the sewer to protect the installation.
![sema_za_cip](https://github.com/user-attachments/assets/d9988c0e-3026-45de-8955-99c5b6030924)
![algoritm_za_rad](https://github.com/user-attachments/assets/12d8f512-66c1-4584-bdab-348e5229a989)
