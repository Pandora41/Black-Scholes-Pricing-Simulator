# 📊 Black-Scholes Pricing Simulator
"The market is a random walk, but the math behind it is beautifully deterministic."

I built this because I was learning PDE (Partial Differential Equations) and thought: *"Wait, this is literally how Wall Street prices options?"* So I decided to implement Black-Scholes from scratch and see if I could beat the market with math.

Spoiler: I can't. But I learned a lot.

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![NumPy](https://img.shields.io/badge/NumPy-1.24+-green)
![SciPy](https://img.shields.io/badge/SciPy-1.10+-orange)
![Status](https://img.shields.io/badge/status-learning-yellow)
![License](https://img.shields.io/badge/license-MIT-green)

## 🔬 What Even Is This?

Black-Scholes is THE equation that revolutionized finance. Fischer Black, Myron Scholes, and Robert Merton won the Nobel Prize for it in 1997.

Most people use Black-Scholes as a black box: plug in numbers, get option price. But I wanted to understand **what's actually happening under the hood**.

So this simulator:
- Implements Black-Scholes formula from scratch
- Runs Monte Carlo simulations to visualize price paths
- Shows the Gaussian distribution that emerges from random walks
- Lets you experiment with volatility, strike price, and time to expiration

Three questions this answers:
| Question | Answer |
|----------|--------|
| What's a "fair" price for an option? | Black-Scholes formula |
| How does volatility affect option price? | Higher σ = higher premium |
| Can we predict stock prices? | No, but we can model the uncertainty |

That last one is the uncomfortable truth.

## 🧠 The Big Idea (No Finance Degree Required)

### Why do options exist?

Imagine you want to buy BBCA stock, but you're not sure if the price will go up or down. You could:
1. Buy the stock now and hope for the best
2. Buy an **option** — the right (but not obligation) to buy at a fixed price later

If BBCA goes up, you exercise the option and profit. If it goes down, you just lose the option premium. Limited downside, unlimited upside.

### What's a "strike price"?

The strike price (K) is the price you agree to buy/sell at in the future. If current price is 5550 IDR and strike is 6105 IDR, you're betting BBCA will go up by 10%.

### What's "volatility" (σ)?

Volatility measures how much the price bounces around. High volatility = price swings wildly = options are more expensive (because uncertainty is higher).

### How Black-Scholes works

The Black-Scholes formula is a **partial differential equation** (PDE) that describes how option prices evolve over time:

$$\frac{\partial V}{\partial t} + \frac{1}{2}\sigma^2 S^2 \frac{\partial^2 V}{\partial S^2} + rS \frac{\partial V}{\partial S} - rV = 0$$

Where:
- $V$ = option price
- $S$ = stock price
- $t$ = time
- $\sigma$ = volatility
- $r$ = risk-free rate

The solution gives us the "fair" price for a European call option:

$$C = S \cdot N(d_1) - K \cdot e^{-rT} \cdot N(d_2)$$

Where $N(x)$ is the cumulative distribution function of the standard normal distribution (the Gaussian bell curve).

### The Gaussian Connection

Here's the beautiful part: if you simulate thousands of random stock price paths using **Geometric Brownian Motion**, the final prices form a **Gaussian distribution**.

This is the **Central Limit Theorem** in action: millions of random buy/sell decisions create a predictable pattern.

Code: [`black_scholes.py`](black_scholes.py)

## ⚔️ The Simulations

### Simulation 1 — Monte Carlo Paths

We generate 1000 random price paths over 252 trading days using Geometric Brownian Motion:

$$S_{t+\Delta t} = S_t \cdot \exp\left(\left(r - \frac{\sigma^2}{2}\right)\Delta t + \sigma \sqrt{\Delta t} \cdot Z\right)$$

Where $Z \sim \mathcal{N}(0, 1)$ is a standard normal random variable.

Each path is different, but together they show the **range of possible outcomes**.

Code: [`monte_carlo.py`](monte_carlo.py)

### Simulation 2 — Black-Scholes Pricing

For BBCA.JK with:
- Current price: 5550 IDR
- Strike price: 6105 IDR (10% above current)
- Volatility: 33.40%
- Time to expiration: 1 year
- Risk-free rate: 5%

Results:
| Option Type | Price |
|-------------|-------|
| Call | 631.40 IDR |
| Put | 888.66 IDR |

**Interpretation:** If the market offers a call option for less than 631.40 IDR, it's **undervalued** (buy it). If more, it's **overvalued** (don't buy).

Code: [`black_scholes.py`](black_scholes.py) — `black_scholes()` function

### Simulation 3 — Gaussian Distribution

After running 1000 Monte Carlo simulations, we plot the distribution of final prices:

Mean: 5845.45 IDR
Median: 5552.82 IDR
Std Dev: 2017.25 IDR
Probability (Price > Strike): 39.00%


The distribution is **approximately Gaussian** (bell-shaped), confirming the Central Limit Theorem.

Code: [`monte_carlo.py`](monte_carlo.py) — visualization section

## 🔩 Why This Matters for Finance

### The Efficient Market Hypothesis

Black-Scholes assumes markets are **efficient** — all available information is already reflected in the price. This means:
- You can't consistently beat the market
- Stock prices follow a **random walk**
- Future prices are unpredictable

But wait... if markets are efficient, how do hedge funds like Renaissance Technologies (Jim Simons) make 66% annual returns?

**Answer:** They find **inefficiencies** that others miss. Black-Scholes is a model, not reality. Real markets have:
- Fat tails (extreme events happen more often than Gaussian predicts)
- Volatility clustering (high volatility periods follow high volatility)
- Behavioral biases (fear, greed, FOMO)

### The Greeks

Black-Scholes gives us more than just option prices. It also gives us the **Greeks** — sensitivities to different parameters:

| Greek | Symbol | Meaning |
|-------|--------|---------|
| Delta | $\Delta$ | Sensitivity to stock price |
| Gamma | $\Gamma$ | Rate of change of Delta |
| Theta | $\Theta$ | Time decay |
| Vega | $\nu$ | Sensitivity to volatility |
| Rho | $\rho$ | Sensitivity to interest rate |

These Greeks are used by traders to **hedge** their positions and manage risk.

Code: Future work (see below)

## 📓 What the Notebook Shows

Open [`test.ipynb`](test.ipynb) in Jupyter:

| Section | What it does |
|---------|--------------|
| 1 — Data Download | Fetches BBCA.JK data from Yahoo Finance |
| 2 — Parameter Calculation | Computes volatility, strike price, etc. |
| 3 — Monte Carlo Simulation | Generates 1000 random price paths |
| 4 — Visualization | Plots sample paths and final price distribution |
| 5 — Statistics | Computes mean, median, std dev, probability |
| 6 — Black-Scholes Pricing | Calculates call and put option prices |

## 📁 Project Structure
├── main.ipynb ← Jupyter notebook with full demo
├── README.md ← This file
├── requirements.txt ← Python dependencies
└── LICENSE ← MIT License


## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/yourusername/black-scholes-simulator.git
cd black-scholes-simulator

# Install dependencies
pip install -r requirements.txt

# Run the notebook
jupyter notebook main.ipynb

```
### ⚠️ Limitations (Being Honest)
Black-Scholes assumes:
Constant volatility — In reality, volatility changes over time (volatility clustering)
Log-normal distribution — Real markets have fat tails (extreme events happen more often)
No dividends — Many stocks pay dividends, which affects option prices
European options only — Can only exercise at expiration, not before
No transaction costs — Real trading has fees, slippage, etc.
Continuous trading — Markets close, gaps happen overnight
This simulator:
Doesn't implement the Greeks (Delta, Gamma, Theta, Vega, Rho)
Doesn't support American options (Binomial Tree or Finite Difference methods needed)
Doesn't account for dividends
Uses simplified Geometric Brownian Motion (no jumps, no stochastic volatility)
The uncomfortable truth: Black-Scholes is a model, not reality. It's useful, but it's not perfect. As George Box said: "All models are wrong, but some are useful."
### 🔭 Future Work
Greeks implementation — Calculate Delta, Gamma, Theta, Vega, Rho
American options — Binomial Tree method for early exercise
Dividend-adjusted Black-Scholes — Merton's extension
Stochastic volatility — Heston model
Jump-diffusion — Merton's jump-diffusion model
Implied volatility — Reverse-engineer σ from market prices
Interactive dashboard — Streamlit app for real-time experimentation
More assets — Support for crypto, forex, commodities
Backtesting — Test Black-Scholes predictions against historical data

### 📄 References
Black, F. & Scholes, M. (1973). The Pricing of Options and Corporate Liabilities. Journal of Political Economy, 81(3), 637-654.
Merton, R.C. (1973). Theory of Rational Option Pricing. Bell Journal of Economics and Management Science, 4(1), 141-183.
Hull, J.C. (2018). Options, Futures, and Other Derivatives (10th ed.). Pearson.
Shreve, S.E. (2004). Stochastic Calculus for Finance II: Continuous-Time Models. Springer.
Taleb, N.N. (2007). The Black Swan: The Impact of the Highly Improbable. Random House.
###🐱 About
Made by someone who was learning PDE and thought: "Wait, this is literally how Wall Street prices options?"
Turns out, yes. The same math that describes heat diffusion also describes option pricing. The universe is weird like that.
Fun fact: Isaac Newton lost a fortune in the South Sea Bubble of 1720 because he couldn't predict "the madness of people." Black-Scholes tries to fix that with math. It doesn't always work, but it's a start.


### 📊 Results
Example: BBCA.JK Call Option
Parameters:
Current Price: 5550 IDR
Strike Price: 6105 IDR
Volatility: 33.40%
Time to Expiration: 1 year
Risk-free Rate: 5%

## Output:

=== BLACK-SCHOLES PRICING ===
Call Option Price: 631.40 IDR
Put Option Price: 888.66 IDR

=== MONTE CARLO SIMULATION ===
Mean Final Price: 5845.45 IDR
Median Final Price: 5552.82 IDR
Std Dev Final Price: 2017.25 IDR
Probability (Price > Strike): 39.00%
