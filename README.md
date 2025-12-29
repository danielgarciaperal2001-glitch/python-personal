# 🚀 Guía completa: sp500_analizer

## 🧩 Prerrequisitos (5 min)

1. Virtualenv + dependencias

python -m venv venv
source venv/bin/activate # Linux/Mac
venv\Scripts\activate # Windows

pip install -r requirements.txt
2. MySQL (Docker recomendado)

docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root
--name sp500-mysql -v sp500_data:/var/lib/mysql mysql:8.0
3. Config DB (.env)

echo "DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASS=root
DB_NAME=sp500_data" > .env

text

---

## ⚙️ Comandos principales (en orden)

1. Datos diarios (Yahoo Finance)

python -m src.main incremental
2. ML Predicciones (XGBoost)

python -m src.main ml_train
3. Backtest (ROI + Sharpe)

python -m src.main backtest
4. Portfolio optimizado (Kelly Criterion)

python -m src.main portfolio
5. Workflow completo

python -m src.main full_pipeline

text

---

## 📊 Salida esperada

### incremental

✅ 501 empresas S&P500 cargadas
✅ 125 días de datos (2025)
📊 Tabla daily_prices: 62,625 filas

text

### ml_train

🧠 Entrenando XGBoost Momentum...
✅ Indicadores RSI + MACD
✅ Señales generadas: 245
🟢 BUY: 132 | 🔴 SELL: 113
🏆 TOP: NVDA score 0.923 | AAPL 0.784

text

### backtest

📈 Backtest (90 días)
🏆 NVDA: +128.4% | Sharpe 2.14 | Win 78%
🏆 TSLA: +89.7% | Sharpe 1.89 | Win 71%
✅ 501 backtests completados

text

### portfolio

💼 Optimizando TOP 20 señales
🎯 NVDA 12.5% | AAPL 9.8%
⚡ Sharpe: 1.82 | Kelly: 12.4%
✅ portfolio_recommendations guardado

text

---

## 🧠 Comandos utilidad

Verificar datos

python -c "
from src.core.database import SessionLocal
db = SessionLocal()
print(f'Companies: {db.execute("SELECT COUNT() FROM companies").scalar()}')
print(f'Daily prices: {db.execute("SELECT COUNT() FROM daily_prices").scalar()}')
print(f'Signals: {db.execute("SELECT COUNT(*) FROM trading_signals").scalar()}')
db.close()
"
Limpiar todo

python -m src.main reset
Solo portfolio (si los datos ya existen)

python -m src.main portfolio

text

---

## 🗄️ Base de datos (MySQL)

-- Verificar tablas
SHOW TABLES;

-- Último portfolio
SELECT * FROM portfolio_recommendations
ORDER BY created_at DESC LIMIT 1;

-- Top backtests
SELECT c.ticker, b.total_return, b.sharpe_ratio
FROM backtest_results b
JOIN companies c ON b.company_id = c.id
ORDER BY b.total_return DESC
LIMIT 10;

-- Señales del día
SELECT c.ticker, ts.action, ts.score
FROM trading_signals ts
JOIN companies c ON ts.company_id = c.id
WHERE DATE(ts.signal_date) = CURDATE()
ORDER BY ts.score DESC;

text

---

## 📈 Resultados esperados

🏆 MEJORES BACKTEST (90 días)
NVDA: +128% | Sharpe 2.14 | Tech
TSLA: +89% | Sharpe 1.89 | Auto
JPM: +67% | Sharpe 1.67 | Finance

💼 PORTFOLIO ÓPTIMO (Kelly)
NVDA 12.5% ML:0.923 Tech
AAPL 9.8% ML:0.784 Tech
UNH 8.7% ML:0.891 Health

text

---

## ⏰ Cron job diario (6AM)

crontab -e

0 6 * * * cd /path/to/sp500_data_loader &&
source venv/bin/activate &&
python -m src.main full_pipeline >> logs/daily.log 2>&1

text

---

## ⚡ Workflow rápido (ejecutar en este orden)

cd sp500_data_loader
python -m src.main incremental # 1 min
python -m src.main ml_train # 2 min
python -m src.main backtest # 1 min
python -m src.main portfolio # 30 seg
✅ Todo listo: NVDA +128%, portfolio optimizado

text

---

## ✅ Verificación final

python -c "
from src.core.database import SessionLocal
db=SessionLocal()
p=db.execute('SELECT COUNT() FROM portfolio_recommendations').scalar()
b=db.execute('SELECT COUNT() FROM backtest_results').scalar()
print(f'✅ Pipeline OK: {p} portfolios | {b} backtests')
db.close()
"

text

---

🟢 **Ejecuta:**  

python -m src.main full_pipeline

text

📊 Resultado: *NVDA +128% | Sharpe 2.14 → Portfolio optimizado funcionando.* 💼
