
# Atividade do bot no Telegram

Nosso grupo (formado por Denis, Diogo, Matheus Henrique e Sara) fizemos um bot no Telegram que, ao digitar o nome de uma cidade, retorna o clima.

**Link para a conversa do Telegram:** t.me/climaThiagobot

Para começar, digite `/start`.

---

## Código em Python utilizado:

``` {python}
import requests
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, ContextTypes, filters

TELEGRAM_TOKEN = "SEU_TOKEN_AQUI"
OPENWEATHER_KEY = "SUA_CHAVE_AQUI"

def clima(cidade):
    url = f"http://api.openweathermap.org/data/2.5/weather?q={cidade},br&appid={OPENWEATHER_KEY}&units=metric&lang=pt_br"
    w = requests.get(url).json()

    if w.get("cod") != 200:
        return f"Não encontrei a cidade '{cidade.title()}'. Tente outro nome."
        
    desc = w["weather"][0]["description"].capitalize()
    temp = w["main"]["temp"]
    feels = w["main"]["feels_like"]
    hum = w["main"]["humidity"]

    return f"**{cidade.title()}**: {desc}, 🌡️ {temp:.1f}°C (Sensação {feels:.1f}°C), 💧 Umidade {hum}%"

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Olá! Digite o nome de uma cidade brasileira para ver o clima 🌤️")

async def resposta(update: Update, context: ContextTypes.DEFAULT_TYPE):
    cidade = update.message.text.strip()
    if not cidade:
        await update.message.reply_text("Por favor, digite um nome de cidade válido.")
        return
        
    resultado = clima(cidade)
    await update.message.reply_text(resultado, parse_mode='Markdown')

def main():
    app = ApplicationBuilder().token(TELEGRAM_TOKEN).build()
    
    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, resposta))
    
    print("Bot rodando! Pressione Ctrl+C para parar.")
    app.run_polling()

if __name__ == "__main__":
    main()
