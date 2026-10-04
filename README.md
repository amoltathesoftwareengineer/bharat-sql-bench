# bharat-sql-bench

A rigorous open benchmark for evaluating text-to-SQL models on Marathi, Hindi and code-mixed Indian-language questions.

Schema-grounded test sets, execution-accuracy scoring and a public leaderboard to measure how well LLMs serve non-English database users.

> **Status:** early development. The dataset, evaluation harness and leaderboard are being built in the open. See the [Roadmap](#roadmap).

---

## Why this exists

Most text-to-SQL benchmarks (Spider, BIRD and others) are in English. But a large share of people who work with data in India think and ask questions in Marathi, Hindi, or a mix of Indian languages and English.

A model that scores well on English questions can fail badly when the same question is asked in another language. There is no standard, open way to measure this gap for Indian languages.

`bharat-sql-bench` aims to fix that by providing:

- Questions in **Marathi, Hindi and code-mixed** form (for example, Marathi written in Roman script mixed with English).
- Questions grounded in **real database schemas**, so every answer can be checked by running the SQL.
- **Execution-based scoring**, so a query is correct only if it returns the right result, not just if it looks similar.
- A **public leaderboard** so models can be compared fairly.

---

## Example

**Schema (simplified):**

```sql
customers(id, name, city)
orders(id, customer_id, order_date, amount)
products(id, name, price)
order_items(order_id, product_id, quantity)
```

| Language | Question | Gold SQL |
|---|---|---|
| Marathi | २०२४ मध्ये पुणे शहरातील ग्राहकांनी किती ऑर्डर्स दिल्या? | `SELECT COUNT(*) FROM orders o JOIN customers c ON o.customer_id = c.id WHERE c.city = 'Pune' AND strftime('%Y', o.order_date) = '2024';` |
| Hindi | सबसे ज़्यादा बिकने वाले 5 प्रोडक्ट कौन से हैं? | `SELECT p.name FROM products p JOIN order_items oi ON oi.product_id = p.id GROUP BY p.id ORDER BY SUM(oi.quantity) DESC LIMIT 5;` |
| Code-mixed | Last month cha total revenue kiti hota? | `SELECT SUM(amount) FROM orders WHERE strftime('%Y-%m', order_date) = strftime('%Y-%m', 'now', '-1 month');` |

---

## How it works

```
question (mr / hi / mixed) + schema
            |
            v
     model under test
            |
            v
      predicted SQL
            |
            v
  run on SQLite database  -->  compare result with gold SQL result
            |
            v
   execution accuracy + other metrics
```

### Metrics

- **Execution Accuracy (EX):** the predicted query returns the same result as the gold query.
- **Valid SQL rate:** the predicted query runs without error.
- **Per-language breakdown:** scores reported separately for Marathi, Hindi and code-mixed questions.
- **Language gap:** difference between English and Indian-language accuracy on the same questions.

---

## Planned repository structure

```
bharat-sql-bench/
├── data/
│   ├── databases/        # SQLite databases
│   ├── questions/        # questions + gold SQL (mr, hi, mixed, en)
│   └── schema/           # schema descriptions
├── bench/
│   ├── run_model.py      # run a model on the benchmark
│   ├── evaluate.py       # execution-accuracy scoring
│   └── metrics.py
├── leaderboard/          # results and leaderboard page
├── tests/
├── docs/
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

---

## Quickstart (planned)

```bash
git clone https://github.com/amoltathesoftwareengineer/bharat-sql-bench.git
cd bharat-sql-bench
pip install -r requirements.txt

# run a model on the benchmark
python -m bench.run_model --model <model-name> --lang mr

# score the predictions
python -m bench.evaluate --predictions outputs/<model-name>.json
```

---

## Results

Results will be added once the first version of the dataset is released.

| Model | Marathi EX | Hindi EX | Code-mixed EX | English EX |
|---|---|---|---|---|
| _coming soon_ | - | - | - | - |

---

## Roadmap

- [ ] Define schemas and build the SQLite databases
- [ ] Write the first 100 questions with gold SQL in English
- [ ] Translate and adapt to Marathi, Hindi and code-mixed forms
- [ ] Have the questions reviewed by native speakers
- [ ] Build the evaluation harness (execution accuracy)
- [ ] Run baseline models and publish results
- [ ] Launch the public leaderboard
- [ ] Add more schemas and more Indian languages

---

## Contributing

Contributions are welcome, especially:

- New questions in Marathi, Hindi or other Indian languages
- Review of translations by native speakers
- New schemas and databases
- Baseline results for new models

Please open an issue to discuss before sending a large pull request.

---

## License

MIT License. See [LICENSE](LICENSE).

---

## Author

**Amol Tathe**
[LinkedIn](https://linkedin.com/in/amoltathesoftwareengineer) · [GitHub](https://github.com/amoltathesoftwareengineer)
