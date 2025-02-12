# B3-Real-Time-Quotes
Este script em Python permite monitorar as cotações de ativos da Bolsa de Valores de São Paulo (B3) em tempo real, utilizando a biblioteca yfinance para obter dados financeiros e tabulate para formatar a saída em uma tabela.
import yfinance as yf
import pandas as pd
import time
from tabulate import tabulate

def obter_cotacoes(ativos):
    cotações = {}
    for ativo in ativos:
        ticker = f"{ativo}.SA"
        dados = yf.Ticker(ticker).history(period="1d")
        if not dados.empty:
            preco_atual = dados["Close"].iloc[-1]
            variacao = (dados["Close"].iloc[-1] - dados["Open"].iloc[-1]) / dados["Open"].iloc[-1] * 100
            cotações[ativo] = {
                "Preço Atual": preco_atual,
                "Variação (%)": variacao
            }
    return cotações

def exibir_cotacoes(cotações):
    tabela = pd.DataFrame(cotações).T
    print(tabulate(tabela, headers="keys", tablefmt="grid", floatfmt=".2f"))

def main():
    ativos = input("Digite os códigos dos ativos separados por vírgula (ex: PETR4, VALE3, ITUB4): ").split(',')
    ativos = [ativo.strip().upper() for ativo in ativos]
    intervalo = int(input("Digite o intervalo de atualização em segundos (ex: 5): "))
    print("Monitorando cotações em tempo real... Pressione Ctrl+C para sair.")
    try:
        while True:
            cotações = obter_cotacoes(ativos)
            exibir_cotacoes(cotações)
            time.sleep(intervalo)
    except KeyboardInterrupt:
        print("Monitoramento encerrado.")

if __name__ == "__main__":
    main()
