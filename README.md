# Analyzing Trader Performance by Bitcoin Market Sentiment

This project explores how Bitcoin market sentiment (Fear & Greed Index) impacts trader behavior and outcomes. Using historical trade data merged with daily sentiment classification, the code provides insights into trader performance during different emotional phases of the market.


##  Datasets Used

### 1. `fear_greed_index.csv`
- Columns: `timestamp`, `value`, `classification`, `date`
- Contains the **daily sentiment score and label** (e.g., Fear, Greed, Extreme Greed, etc.)

### 2. `historical_data.csv`
- Columns include: `Timestamp IST`, `Closed PnL`, `Size USD`, and more
- Records **individual trades**, including profit/loss and trade sizes



## Code Workflow

### 1. Data Loading & Preprocessing
```python
fear = pd.read_csv("fear_greed_index.csv", parse_dates=["date"])
trades = pd.read_csv("historical_data.csv", parse_dates=["Timestamp IST"], dayfirst=True)
```
- Dates are parsed correctly to allow for merging.

### 2. Merging Sentiment with Trades
```python
trades["date"] = trades["Timestamp IST"].dt.floor("D")
merged = trades.merge(fear[["date", "classification"]], on="date", how="left")
merged = merged.dropna(subset=["classification"])
```
- Adds a `classification` label to each trade, allowing analysis by sentiment.

### 3. Feature Engineering
```python
summary = merged.groupby("classification").agg(
    avg_pnl=("Closed PnL", "mean"),
    win_rate=("Closed PnL", lambda x: (x > 0).mean()),
    avg_size=("Size USD", "mean"),
    trade_count=("Closed PnL", "count")
).reset_index()
```
This summarizes each sentiment class:
- **Average Profit/Loss**
- **Win Rate** (percentage of profitable trades)
- **Average Trade Size**
- **Trade Count**

### 4. Visualization
```python
# 2x2 grid of seaborn bar plots
sns.barplot(data=summary, x="classification", y="avg_pnl")
sns.barplot(data=summary, x="classification", y="win_rate")
sns.barplot(data=summary, x="classification", y="avg_size")
sns.barplot(data=summary, x="classification", y="trade_count")
```
- Offers **intuitive insights** into how sentiment influences performance.

---

##  Interpretation of Results

###  Average PnL
- Traders earned **more profits** during **Extreme Greed** and **Fear** phases.
- **Neutral sentiment** days had the **lowest average profits**.

###  Win Rate
- Win rate was **highest during fear-based sentiments**, indicating traders may be more cautious and strategic.

###  Trade Size
- Trade sizes fluctuated moderately but didn't show extreme variation across sentiments.

###  Trade Volume
- Most trades occurred during **Greed** and **Fear** phases, aligning with emotionally-driven market activity.

---

##  Conclusion

- Emotional states of the market **do affect trader performance**.
- **Extreme sentiments** (Fear/Greed) are often associated with **higher profits and win rates**.
- Neutral days are the **least favorable** for profit-making.

---

##  Tools Used
- Python
- Pandas, Seaborn, Matplotlib
- CSV files

---

##  File Structure
```
- fear_greed_index.csv
- historical_data.csv
- analysis.ipynb (or .py)
- README.md
```
