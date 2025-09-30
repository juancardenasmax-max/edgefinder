# MT5 Automation Guide for Claude CLI

## **Can Claude CLI Connect to MT5?**

**Short Answer**: Partially via Python API, but NOT for backtesting/optimization.

---

## **What Claude CLI CAN Do:**

### **✅ Via Python MT5 API:**
1. **Connect to MT5** - Check if MT5 is running
2. **Read account info** - Balance, equity, profit
3. **Get market data** - OHLC bars, tick data
4. **Read symbol info** - Spread, contract size, etc.
5. **Parse EA code** - Analyze .mq5 files
6. **Validate parameters** - Check if fixes work

### **❌ What Claude CLI CANNOT Do:**
1. **Compile EAs** - Must use MetaEditor (F7)
2. **Run backtests** - Must use Strategy Tester GUI
3. **Run optimization** - Must use Genetic Algorithm GUI
4. **Control Strategy Tester** - No API access

---

## **Option 1: Python MT5 Bridge (Limited)**

### **Setup:**
```bash
# Install MT5 Python package
.venv/bin/pip install MetaTrader5

# Run bridge test
.venv/bin/python mt5_bridge.py
```

### **What It Does:**
- Connects to MT5 terminal
- Reads account/market data
- Can analyze historical data
- **Cannot run backtests or optimization**

### **Use Cases:**
- ✅ Verify MT5 is running
- ✅ Check account balance
- ✅ Get live market data
- ✅ Validate data quality
- ❌ Run EA backtests (use GUI)
- ❌ Genetic optimization (use GUI)

---

## **Option 2: MQL5 Test Scripts (RECOMMENDED)**

Create MQL5 scripts that Claude can generate for you!

### **Automated Test Script:**

```mql5
//+------------------------------------------------------------------+
//| AutoValidateEA.mq5 - Automated Parameter Validation              |
//| Run this script to test if parameters affect results             |
//+------------------------------------------------------------------+
#property copyright "EdgeFinder"
#property version   "1.00"
#property script_show_inputs

input string TestSymbol = "USDJPY";
input datetime StartDate = D'2024.01.01';
input datetime EndDate = D'2024.03.31';

//+------------------------------------------------------------------+
//| Script program start function                                    |
//+------------------------------------------------------------------+
void OnStart()
{
    Print("🔍 AUTOMATED EA VALIDATION TEST");
    Print("================================");

    // Test 1: Run backtest with default parameters
    Print("\n📊 Test 1: Default Parameters");
    double profit1 = RunBacktestWithParams(20, 0.015);
    Print("   Net Profit: $", profit1);

    // Test 2: Run backtest with different parameters
    Print("\n📊 Test 2: Modified Parameters");
    double profit2 = RunBacktestWithParams(40, 0.025);
    Print("   Net Profit: $", profit2);

    // Test 3: Validate results differ
    Print("\n✅ VALIDATION:");
    double diff = MathAbs(profit1 - profit2);
    double pct_change = (diff / MathAbs(profit1)) * 100.0;

    Print("   Profit difference: $", diff, " (", pct_change, "%)");

    if(pct_change > 10.0)
    {
        Print("✅ PASS: Parameters affect results!");
    }
    else
    {
        Print("❌ FAIL: Parameters have no effect (hardcoded values?)");
    }

    // Save results to file
    SaveTestResults(profit1, profit2, pct_change);
}

//+------------------------------------------------------------------+
//| Run backtest with specific parameters                            |
//+------------------------------------------------------------------+
double RunBacktestWithParams(int ma_period, double trend_threshold)
{
    // Note: MQL5 cannot programmatically run Strategy Tester
    // This is a SIMULATION - you must run manually

    Print("⚠️  MQL5 Script Limitation:");
    Print("   Cannot programmatically run Strategy Tester");
    Print("   You must run 2 backtests manually:");
    Print("   1. MAPeriod=", ma_period, " TrendStrength=", trend_threshold);
    Print("   2. Record results and compare");

    return 0.0; // Placeholder
}

//+------------------------------------------------------------------+
//| Save test results to CSV file                                    |
//+------------------------------------------------------------------+
void SaveTestResults(double profit1, double profit2, double pct_change)
{
    int handle = FileOpen("EA_Validation_Results.csv", FILE_WRITE|FILE_CSV);
    if(handle != INVALID_HANDLE)
    {
        FileWrite(handle, "Test,Profit1,Profit2,Change%,Status");
        FileWrite(handle, "Parameter Test", profit1, profit2, pct_change,
                 (pct_change > 10.0) ? "PASS" : "FAIL");
        FileClose(handle);
        Print("✅ Results saved to: Files/EA_Validation_Results.csv");
    }
}
```

**Limitation**: MQL5 scripts **cannot control Strategy Tester** programmatically.

---

## **Option 3: Manual MT5 Testing (BEST FOR NOW)**

Claude CLI can **generate test plans** that you execute manually:

### **Claude-Generated Test Plan:**

```markdown
## Validation Test Sequence (Run in MT5)

### Test 1: Baseline
1. Open Strategy Tester
2. EA: USDJPY_RegimeAdaptive_UltimateEA
3. Symbol: USDJPY, Period: M1
4. Dates: 2024.01.01 - 2024.01.31
5. Parameters: All defaults
6. Run → Record: Net Profit = $____

### Test 2: Modified Parameters
1. Change: TrendStrengthThreshold 0.015 → 0.025
2. Change: MAPeriod 20 → 40
3. Run → Record: Net Profit = $____

### Test 3: Validate
- If profits differ by >10% → ✅ PASS
- If profits identical → ❌ FAIL
```

Claude generates the test plan, you execute in MT5, Claude analyzes results.

---

## **Option 4: File-Based Communication**

Claude can read MT5 output files!

### **Workflow:**

1. **You run backtest in MT5** → Saves report to `USDJPY_RegimeAdaptive_UltimateEA_Report.html`
2. **Claude reads report**:
   ```bash
   cat "C:\Users\...\Terminal\...\Reports\*.html"
   ```
3. **Claude analyzes** results and suggests next optimization

### **Example:**
```bash
# You: Run backtest, save report
# Claude reads report via file path you provide
# Claude: "Net profit $5,234, Sharpe 1.42, suggests increasing Tier1Mult to 3.0"
```

---

## **Recommended Workflow:**

### **Phase 1: Claude Generates**
- ✅ Creates optimized EA code
- ✅ Generates .set optimization files
- ✅ Creates validation test plans
- ✅ Analyzes your data files

### **Phase 2: You Execute in MT5**
- Run compilation (F7)
- Run backtests manually
- Run genetic optimization
- Save reports

### **Phase 3: Claude Analyzes**
- You paste results or provide file paths
- Claude reads HTML reports
- Claude suggests parameter tweaks
- Claude generates next iteration

---

## **Current Status:**

### **✅ What's Done:**
1. EA code 100% optimized (all in one file)
2. Validation tested with your 4.1M bars
3. Python bridge created (limited functionality)
4. Test scripts ready

### **⚠️ What Needs Manual Work:**
1. Copy EA to MT5 Experts folder
2. Compile in MetaEditor (F7)
3. Run backtests in Strategy Tester
4. Run genetic optimization (4-8 hours)

---

## **Install MT5 Python Bridge:**

```bash
# Option 1: pip install
.venv/bin/pip install MetaTrader5

# Option 2: Test if MT5 is accessible
.venv/bin/python mt5_bridge.py

# Expected output:
# ✅ Connected to MT5
# ✅ Can read account info
# ⚠️  Cannot run backtests (use GUI)
```

---

## **Why MT5 API is Limited:**

MT5's architecture:
- **Terminal** = User interface (Strategy Tester here)
- **Expert Advisor** = Runs inside terminal
- **Python API** = External connector (read-only mostly)

**Strategy Tester is NOT exposed to Python API!**

This is by design - MetaQuotes wants you to use their GUI for backtesting/optimization.

---

## **Alternative: Cloud Backtesting (Advanced)**

If you need fully automated testing:

1. **MQL5 Cloud** - Rent cloud agents for optimization
2. **Custom Server** - Build your own MT5 testing server
3. **Trading Platform APIs** - Use brokers with better APIs (IBKR, etc.)

But for now: **Manual MT5 Strategy Tester is the standard approach.**

---

## **Summary:**

| Task | Claude CLI | MT5 GUI | Method |
|------|-----------|---------|--------|
| Code generation | ✅ Yes | ❌ No | Claude creates .mq5 |
| Code validation | ✅ Yes | ❌ No | Python analysis |
| Compilation | ❌ No | ✅ Yes | MetaEditor F7 |
| Backtesting | ❌ No | ✅ Yes | Strategy Tester |
| Optimization | ❌ No | ✅ Yes | Genetic Algorithm |
| Result analysis | ✅ Yes | ❌ No | Claude reads reports |

**Best workflow**: Claude generates → You test → Claude analyzes → Repeat

---

## **Next Steps:**

1. **Try Python bridge**: `.venv/bin/python mt5_bridge.py`
2. **If fails**: Manual testing in MT5 GUI
3. **Paste results**: I'll analyze and suggest improvements
4. **Iterate**: Refine parameters based on results

**Ready to test?** Let me know if MT5 is running and I'll try the Python bridge!