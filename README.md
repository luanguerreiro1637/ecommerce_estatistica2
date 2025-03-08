# ecommerce_estatistica2


import dash
from dash import dcc, html
from dash.dependencies import Input, Output
import pandas as pd
import plotly.express as px
import plotly.graph_objects as go
import os

# Passo 2: Ler o arquivo CSV e carregar os dados em um DataFrame
file_path = 'C:/Users/logue/Documents/ecommerce_estatistica.csv'  # Substitua pelo caminho completo do arquivo

# Verificar se o arquivo existe
if os.path.exists(file_path):
    df = pd.read_csv(file_path)
else:
    print(f"Erro: O arquivo {file_path} não foi encontrado.")
    exit()

# Passo 3: Criar a aplicação Dash
app = dash.Dash(__name__)

app.layout = html.Div([
    html.H1("Análise de Dados de E-commerce", style={'textAlign': 'center'}),
    
    html.Div([
        html.H2("Distribuição das Notas dos Produtos"),
        dcc.Graph(id='histograma-notas')
    ]),
    
    html.Div([
        html.H2("Relação entre Preço e Quantidade de Avaliações"),
        dcc.Graph(id='dispersao-preco-avaliacoes')
    ]),
    
    html.Div([
        html.H2("Mapa de Calor - Correlação entre Variáveis"),
        dcc.Graph(id='mapa-calor')
    ]),
    
    html.Div([
        html.H2("Média das Notas por Gênero"),
        dcc.Graph(id='barra-genero-notas')
    ]),
    
    html.Div([
        html.H2("Distribuição dos Produtos por Temporada"),
        dcc.Graph(id='pizza-temporada')
    ]),
    
    html.Div([
        html.H2("Distribuição de Densidade dos Preços"),
        dcc.Graph(id='densidade-precos')
    ]),
    
    html.Div([
        html.H2("Relação entre Preço e Quantidade de Avaliações com Linha de Regressão"),
        dcc.Graph(id='regressao-preco-avaliacoes')
    ])
])

# Callbacks para atualizar os gráficos
@app.callback(
    Output('histograma-notas', 'figure'),
    Input('histograma-notas', 'id')
)
def update_histograma(_):
    fig = px.histogram(df, x='Nota', nbins=10, title='Distribuição das Notas dos Produtos')
    return fig

@app.callback(
    Output('dispersao-preco-avaliacoes', 'figure'),
    Input('dispersao-preco-avaliacoes', 'id')
)
def update_dispersao(_):
    fig = px.scatter(df, x='Preço', y='N_Avaliações', title='Relação entre Preço e Quantidade de Avaliações')
    return fig

@app.callback(
    Output('mapa-calor', 'figure'),
    Input('mapa-calor', 'id')
)
def update_mapa_calor(_):
    corr = df.corr()
    fig = go.Figure(data=go.Heatmap(z=corr.values, x=corr.columns, y=corr.columns, colorscale='coolwarm'))
    fig.update_layout(title='Mapa de Calor - Correlação entre Variáveis')
    return fig

@app.callback(
    Output('barra-genero-notas', 'figure'),
    Input('barra-genero-notas', 'id')
)
def update_barra_genero_notas(_):
    fig = px.bar(df, x='Gênero', y='Nota', title='Média das Notas por Gênero')
    return fig

@app.callback(
    Output('pizza-temporada', 'figure'),
    Input('pizza-temporada', 'id')
)
def update_pizza_temporada(_):
    fig = px.pie(df, names='Temporada', title='Distribuição dos Produtos por Temporada')
    return fig

@app.callback(
    Output('densidade-precos', 'figure'),
    Input('densidade-precos', 'id')
)
def update_densidade_precos(_):
    fig = px.density_contour(df, x='Preço', title='Distribuição de Densidade dos Preços')
    return fig

@app.callback(
    Output('regressao-preco-avaliacoes', 'figure'),
    Input('regressao-preco-avaliacoes', 'id')
)
def update_regressao_preco_avaliacoes(_):
    fig = px.scatter(df, x='Preço', y='N_Avaliações', trendline='ols', title='Relação entre Preço e Quantidade de Avaliações com Linha de Regressão')
    return fig

# Rodar a aplicação
if __name__ == '__main__':
    app.run_server(debug=True)
