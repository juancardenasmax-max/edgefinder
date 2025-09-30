# Next Session Action Plan

## 🎯 **PRIMARY OBJECTIVE**
Rerun complete optimization suite with the FIXED EA (drawdown circuit breaker bug resolved) and validate results.

## 🚨 **CRITICAL CONTEXT**
- **Date of Bug Fix**: 2025-09-30
- **Bug**: maxDrawdown never reset → EA stopped trading permanently after hitting 12% drawdown
- **Impact**: All 15,682 previous optimization results are INVALID (0-2% profit retention)
- **Fix Applied**: Lines 1737-1744 in USDJPY_RegimeAdaptive_UltimateEA.mq5
- **Fix Details**: maxDrawdown resets when account recovers to 95% of daily start balance

## 📋 **EXACT STEPS TO RUN**

### **Step 1: Verify Fixed EA is Compiled**
```bash
# Check that the fixed EA exists
ls -lh USDJPY_RegimeAdaptive_UltimateEA.mq5

# Verify the fix is present (should show lines 1737-1744)
grep -A 8 "CRITICAL FIX: Reset maxDrawdown" USDJPY_RegimeAdaptive_UltimateEA.mq5
```

### **Step 2: Copy to MT5 and Recompile**
```
1. Copy USDJPY_RegimeAdaptive_UltimateEA.mq5 to MT5/MQL5/Experts/
2. Open MetaEditor in MT5
3. Press F7 to compile
4. Verify: "0 errors, 0 warnings"
```

### **Step 3: Run Backward Optimization (2014-2019)**
```
MT5 Strategy Tester Settings:
- EA: USDJPY_RegimeAdaptive_UltimateEA
- Symbol: USDJPY
- Period: M1 (1-minute bars)
- Dates: 2014.01.01 to 2019.12.31 (5 years)
- Model: Every tick based on real ticks
- Optimization: Genetic Algorithm
- Population: 100-200
- Load: EdgeFinder_Genetic_Optimization.set

Expected Time: 4-8 hours
Expected Result: 10,000+ optimization passes with CONTINUOUS trading (not stopping in Feb 2019!)
```

### **Step 4: Identify Best Configuration**
```
Criteria for "BEST" configuration:
- Sharpe Ratio > 1.5
- Profit Factor > 1.3
- Max Drawdown < 15%
- Total Trades > 100 (proves EA didn't stop)
- Profit > $5,000 on $1,000 starting capital

Save this configuration as: "Backward_Best_2014-2019.set"
```

### **Step 5: Run Forward Validation (2019-2024)**
```
MT5 Strategy Tester Settings:
- SAME EA (already compiled)
- Symbol: USDJPY
- Period: M1
- Dates: 2019.01.01 to 2024.12.31 (5 years)
- Model: Every tick based on real ticks
- Optimization: DISABLED (validation only!)
- Load: "Backward_Best_2014-2019.set" (from Step 4)

Expected Time: 30-60 minutes
Expected Result: EA trades CONTINUOUSLY through entire period
```

### **Step 6: Calculate Retention Metrics**
```python
# Use this to analyze results:
backward_profit = $____  # From Step 4
forward_profit = $____   # From Step 5

profit_retention = (forward_profit / backward_profit) * 100
print(f"Profit Retention: {profit_retention:.1f}%")

backward_sharpe = ____   # From Step 4
forward_sharpe = ____    # From Step 5

sharpe_retention = (forward_sharpe / backward_sharpe) * 100
print(f"Sharpe Retention: {sharpe_retention:.1f}%")

# SUCCESS CRITERIA:
# ✅ PASS: profit_retention > 50% AND sharpe_retention > 50%
# ⚠️  MARGINAL: 30% < retention < 50%
# ❌ FAIL: retention < 30% (still overfitted)
```

## 🎯 **EXPECTED OUTCOMES**

### **If Results are GOOD (>50% retention):**
```
✅ Bug fix confirmed working
✅ EA is robust across different market periods
✅ Ready for walk-forward validation (3+ periods)
✅ Ready for live paper trading

Next steps:
1. Run 3-period walk-forward validation
2. Deploy to demo account
3. Monitor for 1-2 weeks
4. Scale to live trading with small positions
```

### **If Results are MARGINAL (30-50% retention):**
```
⚠️  Some overfitting remains, but system is viable
⚠️  May need parameter simplification
⚠️  Consider increasing training period to 6 years

Next steps:
1. Analyze which parameters are unstable
2. Lock stable parameters, re-optimize unstable ones
3. Run extended walk-forward validation
```

### **If Results are STILL BAD (<30% retention):**
```
❌ Deeper issues beyond the circuit breaker bug
❌ Possible causes:
   - Market regime change between periods
   - Too many optimizable parameters (overfitting)
   - Strategy fundamentally not robust

Next steps:
1. Simplify strategy (fewer parameters)
2. Test individual strategies in isolation (TestMode 0-3)
3. Consider different timeframes (15M, 1H instead of 1M)
4. Review regime detection logic for edge cases
```

## 📊 **KEY METRICS TO REPORT**

After running the tests, report these metrics:

```
BACKWARD TEST (2014-2019):
- Net Profit: $____
- Sharpe Ratio: ____
- Profit Factor: ____
- Max Drawdown: ____%
- Total Trades: ____
- Did EA stop trading? YES/NO
- If stopped, when? ____

FORWARD TEST (2019-2024):
- Net Profit: $____
- Sharpe Ratio: ____
- Profit Factor: ____
- Max Drawdown: ____%
- Total Trades: ____
- Did EA stop trading? YES/NO
- If stopped, when? ____

RETENTION ANALYSIS:
- Profit Retention: ____%
- Sharpe Retention: ____%
- Status: PASS / MARGINAL / FAIL

VISUAL CHECK:
- Graph shows continuous growth? YES/NO
- Graph flatlines at any point? YES/NO
- If flatline, at what date? ____
```

## 🔍 **TROUBLESHOOTING**

### **If EA still stops trading:**
```
1. Check MT5 logs for "DRAWDOWN CIRCUIT BREAKER ACTIVATED" messages
2. Check if "✅ DRAWDOWN RESET - Account recovered" appears in logs
3. Verify MaxDrawdownPercent setting (should be 8-12%)
4. Check if fix is actually in compiled EA:
   - Look for "CRITICAL FIX: Reset maxDrawdown" in source code
   - Verify compilation date is AFTER 2025-09-30
```

### **If results are still poor:**
```
Run diagnostic test:
1. Disable circuit breaker: EnableDrawdownBreaker = false
2. Run 1-month test (2024.01.01 - 2024.01.31)
3. Check if EA trades continuously
4. If YES: Circuit breaker is too strict, increase MaxDrawdownPercent
5. If NO: Different issue, check regime detection logic
```

## 💡 **QUICK START COMMAND**

Copy-paste this into your next session:

```
I need to revalidate the EdgeFinder EA after fixing the critical drawdown circuit breaker bug (2025-09-30). The bug caused EA to stop trading permanently after hitting 12% drawdown. All previous optimization results (15,682 passes) showed 0-2% profit retention and are INVALID.

Please help me:
1. Verify the fix is in the compiled EA (lines 1737-1744)
2. Guide me through running backward optimization (2014-2019)
3. Validate with forward test (2019-2024) using SAME parameters
4. Calculate profit and Sharpe retention metrics
5. Determine if results are PASS (>50%), MARGINAL (30-50%), or FAIL (<30%)

The fix allows maxDrawdown to reset when account recovers to 95% of daily start balance. Expected outcome: EA should now trade continuously throughout entire 10-year period instead of stopping in Feb 2019.

Current files:
- USDJPY_RegimeAdaptive_UltimateEA.mq5 (FIXED)
- EdgeFinder_Genetic_Optimization.set (optimization parameters)
- ./test/* (INVALID old results)
```

---

**SAVE THIS FILE**: /home/kingkong/code/edgefinder/NEXT_SESSION_PROMPT.md

**TO USE TOMORROW**:
1. Run `/primer` command
2. Copy-paste the "Quick Start Command" section above
3. Follow the step-by-step guide

---

**Expected Time Commitment:**
- Setup and compilation: 15 minutes
- Backward optimization: 4-8 hours (can run overnight)
- Forward validation: 30-60 minutes
- Analysis and reporting: 30 minutes
- **Total**: 6-10 hours (mostly unattended computer time)

**Success Indicator:**
A continuous upward-sloping equity curve from 2014-2024 with NO flatlines!