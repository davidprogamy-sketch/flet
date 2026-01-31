import flet as ft
import pandas as pd
from datetime import datetime

# Função para buscar histórico do Excel
def obter_historico_excel():
    caminho = "Hora Hora TL1.xlsm"
    try:
        # Lendo a Sheet1 onde os registros são salvos
        df = pd.read_excel(caminho, sheet_name='Sheet1')
        # Pegar os últimos 10 registros para não sobrecarregar a tela
        return df.tail(10).values.tolist()
    except:
        return []

def main(page: ft.Page):
    page.title = "Sistema TL1 - Produção & Histórico"
    page.theme_mode = ft.ThemeMode.DARK
    
    # --- TELA DE REGISTRO (CÓDIGO ANTERIOR) ---
    def view_registro():
        return ft.Column([
            ft.Text("Lançar Produção", size=20, weight="bold"),
            ft.TextField(label="Quantidade", id="qtd"),
            ft.Dropdown(label="Ocorrência", options=[ft.dropdown.Option("Normal"), ft.dropdown.Option("Parada")]),
            ft.ElevatedButton("Salvar no Excel", icon=ft.icons.SAVE)
        ])

    # --- TELA DE HISTÓRICO (NOVA) ---
    def view_historico():
        dados = obter_historico_excel()
        
        # Criando as linhas da tabela com os dados do Excel
        linhas_tabela = []
        for linha in dados:
            linhas_tabela.append(
                ft.DataRow(
                    cells=[
                        ft.DataCell(ft.Text(str(linha[0]))), # Coluna Hora
                        ft.DataCell(ft.Text(str(linha[1]))), # Coluna Qtd
                        ft.DataCell(ft.Text(str(linha[2]))), # Coluna Status/Motivo
                    ]
                )
            )

        return ft.Column([
            ft.Text("Últimos Registros (Sheet1)", size=20, weight="bold"),
            ft.DataTable(
                columns=[
                    ft.DataColumn(ft.Text("Hora")),
                    ft.DataColumn(ft.Text("Qtd")),
                    ft.DataColumn(ft.Text("Evento")),
                ],
                rows=linhas_tabela
            ),
            ft.IconButton(icon=ft.icons.REFRESH, on_click=lambda _: page.update())
        ], scroll=ft.ScrollMode.ADAPTIVE)

    # --- NAVEGAÇÃO POR ABAS ---
    tabs = ft.Tabs(
        selected_index=0,
        animation_duration=300,
        tabs=[
            ft.Tab(text="Lançamento", icon=ft.icons.EDIT, content=view_registro()),
            ft.Tab(text="Histórico", icon=ft.icons.HISTORY, content=view_historico()),
        ],
        expand=1
    )

    page.add(tabs)

ft.app(target=main)
