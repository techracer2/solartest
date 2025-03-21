Here are the key assumptions made in the simulation code, organized by category for clarity. These should be added to your README to help collaborators understand the model's boundaries and limitations:

---

### **System Configuration Assumptions**
1. **Component Availability**  
   - Diesel generator can be enabled/disabled via `diesel_enabled` flag  
   - Battery storage (BESS) is always present and operational  
   - Grid connection is always available with bidirectional power flow  

2. **Power Flow Constraints**  
   - Solar/diesel ➔ unidirectional flow (cannot reverse)  
   - Grid/BESS ➔ bidirectional flow  
   - Diesel cannot export to grid (`diesel_on + grid_export ≤ 1`)  

---

### **Data Assumptions**
3. **Input Data Requirements**  
   - Solar/load data: 15-min resolution CSV files with columns:  
     ```csv
     timestamp,power
     2023-01-01 00:00:00,10.5
     ```
   - Missing data handled via forward-fill → zeros after last valid point  
   - Default to synthetic data if CSVs not found/malformed  

4. **Temporal Resolution**  
   - Fixed 15-minute time steps (`model.time_step = 0.25` hours)  
   - No sub-interval dynamics modeled within 15-minute blocks  

---

### **Component Behavior Assumptions**
5. **Solar PV System**  
   - Zero curtailment (all available solar must be consumed/stored/exported)  
   - Instantaneous response (no ramp-up/down constraints)  

6. **Diesel Generator**  
   - No minimum uptime/downtime constraints  
   - Constant efficiency (no partial load efficiency curves)  
   - Fixed marginal cost (`diesel_price` $/kWh)  

7. **Battery (BESS)**  
   - Linear degradation (no cycle aging or temperature effects)  
   - Constant charge/discharge efficiency (`bess_efficiency`)  
   - No self-discharge during idle periods  

---

### **Economic Assumptions**
8. **Pricing**  
   - Fixed grid import price (`grid_price`)  
   - Grid export compensation = 80% of import price  
   - No time-of-use (TOU) tariffs or demand charges  

9. **Cost Calculations**  
   - Diesel fuel cost proportional to energy output  
   - No fixed costs for generator startup/shutdown  

---

### **Operational Assumptions**
10. **Power Balance**  
    - Perfect nodal balance:  
      `Solar + Diesel + Grid_import + BESS_discharge = Load + Grid_export + BESS_charge`  
    - No transmission losses or voltage constraints  

11. **Control Logic**  
    - Battery cannot charge/discharge simultaneously (`bess_charging` binary)  
    - No state-of-charge (SOC) carryover between optimization horizons  

---

### **Technical Implementation Assumptions**
12. **Solver Behavior**  
    - Fallback solver order: Gurobi → GLPK → CBC  
    - MIPGap tolerance = 1% (`solver.options['MIPGap'] = 0.01`)  
    - 5-minute solver time limit (`solver.options['TimeLimit'] = 300`)  

13. **Visualization**  
    - Interactive plots assume Jupyter/HTML renderer availability  
    - Color scheme:  
      - Solar = gold  
      - Grid = blue  
      - Battery = green  
      - Diesel = brown  

---

### **Model Simplifications**
14. **Physics Omissions**  
    - No AC/DC conversion losses  
    - No reactive power considerations  
    - Ambient temperature effects ignored  

15. **Market Interactions**  
    - No ancillary service participation  
    - No capacity markets or reserve requirements  

---

### **Validation Assumptions**
16. **Perfect Forecast**  
    - Solar/load data treated as perfect predictions  
    - No forecast error modeling  

17. **Steady-State Operation**  
    - Transient dynamics (e.g., generator ramp-up) not modeled  

---

### **Sample Data Assumptions**
18. **Synthetic Data Generation**  
    - Solar: Gaussian curve peaking at noon (100 kW max)  
    - Load: Dual-peak (morning/evening) + random noise (±20%)  
    - 24-hour horizon by default  
