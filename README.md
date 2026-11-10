import sqlite3
import tkinter as tk
from tkinter import messagebox


# --- Configuração do Banco de Dados ---
def inicializar_banco():
    conexao = sqlite3.connect("clientes.db")
    cursor = conexao.cursor()
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS clientes (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nome TEXT NOT NULL,
            email TEXT NOT NULL,
            telefone TEXT NOT NULL
        )
    """)
    conexao.commit()
    conexao.close()


# --- Funcionalidades ---
def limpar_formulario():
    entry_nome.delete(0, tk.END)
    entry_email.delete(0, tk.END)
    entry_telefone.delete(0, tk.END)
    entry_nome.focus()  # Coloca o cursor de volta no primeiro campo


def salvar_cliente():
    nome = entry_nome.get().strip()
    email = entry_email.get().strip()
    telefone = entry_telefone.get().strip()

    # 4) Validação: verifica se todos os campos estão preenchidos
    if not nome or not email or not telefone:
        messagebox.showwarning(
            "Campos Obrigatórios",
            "Por favor, preencha todos os campos antes de salvar.",
        )
        return

    try:
        # 2) Inserção no Banco de Dados SQLite
        conexao = sqlite3.connect("clientes.db")
        cursor = conexao.cursor()
        cursor.execute(
            "INSERT INTO clientes (nome, email, telefone) VALUES (?, ?, ?)",
            (nome, email, telefone),
        )
        conexao.commit()
        conexao.close()

        # Mensagem de sucesso e limpeza automática dos campos
        messagebox.showinfo("Sucesso", "Cliente cadastrado com sucesso!")
        limpar_formulario()

    except Exception as erro:
        messagebox.showerror(
            "Erro no Banco de Dados", f"Não foi possível salvar os dados: {erro}"
        )


# --- 1) Interface Gráfica (Tkinter) ---
inicializar_banco()

janela = tk.Tk()
janela.title("Cadastro de Clientes")
janela.geometry("360x260")
janela.resizable(False, False)

# Frame principal para organizar os elementos com espaçamento
frame = tk.Frame(janela, padx=20, pady=20)
frame.pack(fill="both", expand=True)

# Campo Nome
tk.Label(frame, text="Nome:").grid(row=0, column=0, sticky="w", pady=5)
entry_nome = tk.Entry(frame, width=32)
entry_nome.grid(row=0, column=1, pady=5)

# Campo E-mail
tk.Label(frame, text="E-mail:").grid(row=1, column=0, sticky="w", pady=5)
entry_email = tk.Entry(frame, width=32)
entry_email.grid(row=1, column=1, pady=5)

# Campo Telefone
tk.Label(frame, text="Telefone:").grid(row=2, column=0, sticky="w", pady=5)
entry_telefone = tk.Entry(frame, width=32)
entry_telefone.grid(row=2, column=1, pady=5)

# Frame para os botões
frame_botoes = tk.Frame(frame)
frame_botoes.grid(row=3, column=0, columnspan=2, pady=20)

# Botão Salvar
btn_salvar = tk.Button(
    frame_botoes,
    text="Salvar Cliente",
    command=salvar_cliente,
    bg="#4CAF50",
    fg="white",
    width=14,
)
btn_salvar.pack(side="left", padx=8)

# Botão Limpar
btn_limpar = tk.Button(
    frame_botoes,
    text="Limpar",
    command=limpar_formulario,
    bg="#f44336",
    fg="white",
    width=14,
)
btn_limpar.pack(side="left", padx=8)

# Inicia o loop da janela
janela.mainloop()
