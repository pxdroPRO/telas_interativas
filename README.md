[main.py](https://github.com/user-attachments/files/23785991/main.py)
# main.py
import tkinter as tk
from tkinter import messagebox
import time

# ---------------- Configuração de tema (modo claro / escuro) ----------------
THEMES = {
    "light": {
        "bg": "#f2f2f2",
        "fg": "#111111",
        "card": "#ffffff",
        "button_bg": "#4a90e2",
        "button_fg": "#ffffff",
        "accent": "#2b7be4"
    },
    # Modo escuro "A" - preto total estilo "gamer"
    "dark": {
        "bg": "#000000",
        "fg": "#ffffff",
        "card": "#0f0f0f",
        "button_bg": "#1e3a8a",   # azul escuro para botões
        "button_fg": "#ffffff",
        "accent": "#2563eb"
    }
}

# ---------------- Aplicação ----------------
class App:
    def __init__(self, root):
        self.root = root
        self.root.title("Projeto Seminário")
        self.root.geometry("380x560")
        self.root.resizable(False, False)

        # dados da aplicação (em memória)
        self.itens = ["Item 1", "Item 2", "Item 3"]
        self.usuario = "Aluno Exemplo"
        self.profile_email = "email@exemplo.com"

        # tema inicial
        self.theme_name = "light"   # default, pode mudar em Configurações
        self.colors = THEMES[self.theme_name]

        # para re-renderizar tela atual ao trocar tema
        self.current_screen = None
        self.current_args = ()

        # container principal (para limpar e redesenhar)
        self.main_frame = tk.Frame(self.root, bg=self.colors["bg"])
        self.main_frame.pack(fill="both", expand=True)

        # inicializar na tela de carregamento e depois login
        self.show_loading(self.tela_login, delay=700, first_load=True)

    # ---------------- utilitários de tema / widgets ----------------
    def set_theme(self, theme_name):
        if theme_name not in THEMES:
            return
        self.theme_name = theme_name
        self.colors = THEMES[theme_name]
        # atualizar janela e redesenhar tela atual
        self.main_frame.configure(bg=self.colors["bg"])
        # re-renderiza a tela atual (mantendo args)
        if self.current_screen:
            # mostrar loading quando trocar tema também para deixar visual agradável
            self.show_loading(lambda: self.current_screen(*self.current_args), delay=400)

    def titulo(self, texto):
        return tk.Label(self.main_frame, text=texto, font=("Arial", 20, "bold"),
                        bg=self.colors["bg"], fg=self.colors["fg"])

    def botao(self, texto, comando, width=22, height=2):
        return tk.Button(self.main_frame, text=texto, command=comando,
                         width=width, height=height,
                         bg=self.colors["button_bg"], fg=self.colors["button_fg"],
                         font=("Arial", 11, "bold"), bd=0, activebackground=self.colors["accent"])

    def limpar_main(self):
        for w in self.main_frame.winfo_children():
            w.destroy()

    # ---------------- Loading (aparece sempre que trocar de tela) ----------------
    def show_loading(self, next_func, delay=500, *args, first_load=False):
        # delay em ms
        loading = tk.Toplevel(self.root)
        loading.overrideredirect(True)
        loading.geometry("220x100")
        # centraliza
        x = self.root.winfo_x() + (self.root.winfo_width() - 220)//2
        y = self.root.winfo_y() + (self.root.winfo_height() - 100)//2
        loading.geometry(f"+{x}+{y}")

        # define estilo do loading de acordo com tema (background leve)
        lb_bg = "#111111" if self.theme_name == "dark" else "#ffffff"
        lb_fg = "#ffffff" if self.theme_name == "dark" else "#000000"
        loading.configure(bg=lb_bg)

        lbl = tk.Label(loading, text="Carregando...", font=("Arial", 12), bg=lb_bg, fg=lb_fg)
        lbl.pack(expand=True, fill="both")

        # after para simular carregamento
        def continuar():
            loading.destroy()
            # chama a função de tela
            next_func(*args)
        # se for a primeira abertura, dá um pouco mais de delay
        d = delay if not first_load else delay + 300
        self.root.after(d, continuar)

    # ---------------- NAVEGAÇÃO: todas as trocas de tela devem usar go_to ----------------
    def go_to(self, screen_func, *args):
        # guarda para re-renderizar ao trocar tema
        self.current_screen = screen_func
        self.current_args = args
        # mostra loading e depois a tela
        self.show_loading(lambda: screen_func(*args), delay=500)

    # ---------------- TELAS ----------------
    def tela_login(self):
        self.limpar_main()
        # marca tela atual
        self.current_screen = self.tela_login
        self.current_args = ()

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Tela de Login").pack(pady=25)

        lbl_user = tk.Label(self.main_frame, text="Usuário:", bg=self.colors["bg"], fg=self.colors["fg"])
        lbl_user.pack()
        self.usuario_entry = tk.Entry(self.main_frame, width=30)
        self.usuario_entry.pack(pady=5)

        lbl_pass = tk.Label(self.main_frame, text="Senha:", bg=self.colors["bg"], fg=self.colors["fg"])
        lbl_pass.pack()
        self.senha_entry = tk.Entry(self.main_frame, show="*", width=30)
        self.senha_entry.pack(pady=5)

        # botão entrar
        self.botao("Entrar", self._login_action).pack(pady=18)
        # atalhos
        btn_demo = tk.Button(self.main_frame, text="Entrar como demo", command=self._login_demo,
                             width=20, bg=self.colors["card"], fg=self.colors["fg"], bd=1)
        btn_demo.pack(pady=6)

        # pequena ajuda
        tk.Label(self.main_frame, text="(Preencha algo e clique Entrar)", bg=self.colors["bg"], fg=self.colors["fg"],
                 font=("Arial", 9)).pack(pady=6)

    def _login_action(self):
        u = self.usuario_entry.get().strip()
        s = self.senha_entry.get().strip()
        if u == "" or s == "":
            messagebox.showerror("Erro", "Preencha todos os campos!")
            return
        # simula autenticação
        self.usuario = u
        self.go_to(self.tela_home)

    def _login_demo(self):
        self.usuario = "Aluno Demo"
        self.profile_email = "demo@exemplo.com"
        self.go_to(self.tela_home)

    def tela_home(self):
        self.limpar_main()
        self.current_screen = self.tela_home
        self.current_args = ()

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Tela Home").pack(pady=18)
        # boas vindas
        tk.Label(self.main_frame, text=f"Olá, {self.usuario}!", bg=self.colors["bg"], fg=self.colors["fg"],
                 font=("Arial", 12)).pack(pady=6)

        # botoes de navegação
        self.botao("Abrir Lista", lambda: self.go_to(self.tela_lista)).pack(pady=8)
        self.botao("Adicionar Item", lambda: self.go_to(self.tela_adicionar_item)).pack(pady=8)
        self.botao("Perfil", lambda: self.go_to(self.tela_perfil)).pack(pady=8)
        self.botao("Configurações", lambda: self.go_to(self.tela_configuracoes)).pack(pady=8)
        self.botao("Sair", lambda: self.go_to(self.tela_login)).pack(pady=12)

    def tela_lista(self):
        self.limpar_main()
        self.current_screen = self.tela_lista
        self.current_args = ()

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Lista de Itens").pack(pady=14)

        # lista em forma de "card" botão para cada item com menu lateral para editar/excluir
        list_frame = tk.Frame(self.main_frame, bg=self.colors["bg"])
        list_frame.pack(pady=6)

        if not self.itens:
            tk.Label(list_frame, text="Nenhum item. Adicione um!", bg=self.colors["bg"], fg=self.colors["fg"]).pack()
        else:
            for item in self.itens:
                card = tk.Frame(list_frame, bg=self.colors["card"], bd=0, relief="ridge", padx=6, pady=6)
                card.pack(fill="x", padx=12, pady=6)

                lbl = tk.Label(card, text=item, bg=self.colors["card"], fg=self.colors["fg"], font=("Arial", 12))
                lbl.pack(side="left", padx=4)

                btns = tk.Frame(card, bg=self.colors["card"])
                btns.pack(side="right")
                tk.Button(btns, text="Abrir", command=lambda i=item: self.go_to(self.tela_detalhes, i),
                          bg=self.colors["button_bg"], fg=self.colors["button_fg"], bd=0).pack(side="left", padx=4)
                tk.Button(btns, text="Editar", command=lambda i=item: self.go_to(self.tela_editar_item, i),
                          bg=self.colors["button_bg"], fg=self.colors["button_fg"], bd=0).pack(side="left", padx=4)
                tk.Button(btns, text="Excluir", command=lambda i=item: self.go_to(self.tela_excluir_item, i),
                          bg="#e53935", fg="#fff", bd=0).pack(side="left", padx=4)

        self.botao("Voltar", lambda: self.go_to(self.tela_home)).pack(pady=12)

    def tela_detalhes(self, item):
        self.limpar_main()
        self.current_screen = self.tela_detalhes
        self.current_args = (item,)

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Detalhes do Item").pack(pady=16)
        tk.Label(self.main_frame, text=f"Você abriu: {item}", bg=self.colors["bg"], fg=self.colors["fg"],
                 font=("Arial", 13)).pack(pady=10)

        self.botao("Editar Item", lambda: self.go_to(self.tela_editar_item, item)).pack(pady=6)
        # botão excluir direto na tela de detalhes também
        tk.Button(self.main_frame, text="Excluir Item", command=lambda: self.go_to(self.tela_excluir_item, item),
                  bg="#e53935", fg="#fff", bd=0, width=22, height=2).pack(pady=6)
        self.botao("Voltar", lambda: self.go_to(self.tela_lista)).pack(pady=12)

    def tela_adicionar_item(self):
        self.limpar_main()
        self.current_screen = self.tela_adicionar_item
        self.current_args = ()

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Adicionar Item").pack(pady=16)

        tk.Label(self.main_frame, text="Nome do novo item:", bg=self.colors["bg"], fg=self.colors["fg"]).pack(pady=6)
        self.novo_item_entry = tk.Entry(self.main_frame, width=30)
        self.novo_item_entry.pack(pady=6)
        self.botao("Salvar", self._salvar_novo_item).pack(pady=10)
        self.botao("Voltar", lambda: self.go_to(self.tela_home)).pack(pady=8)

    def _salvar_novo_item(self):
        nome = self.novo_item_entry.get().strip()
        if nome == "":
            messagebox.showerror("Erro", "O nome não pode ser vazio!")
            return
        self.itens.append(nome)
        messagebox.showinfo("Sucesso", "Item adicionado!")
        self.go_to(self.tela_lista)

    def tela_editar_item(self, item):
        self.limpar_main()
        self.current_screen = self.tela_editar_item
        self.current_args = (item,)

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Editar Item").pack(pady=16)

        tk.Label(self.main_frame, text=f"Item atual: {item}", bg=self.colors["bg"], fg=self.colors["fg"]).pack(pady=6)
        self.editar_entry = tk.Entry(self.main_frame, width=30)
        self.editar_entry.pack(pady=6)
        self.editar_entry.insert(0, item)

        self.botao("Salvar Edição", lambda: self._salvar_edicao(item)).pack(pady=8)
        self.botao("Voltar", lambda: self.go_to(self.tela_detalhes, item)).pack(pady=6)

    def _salvar_edicao(self, item):
        novo_nome = self.editar_entry.get().strip()
        if novo_nome == "":
            messagebox.showerror("Erro", "O novo nome não pode ser vazio!")
            return
        # atualiza na lista
        try:
            idx = self.itens.index(item)
            self.itens[idx] = novo_nome
            messagebox.showinfo("Sucesso", "Item editado!")
            self.go_to(self.tela_lista)
        except ValueError:
            messagebox.showerror("Erro", "Item não encontrado!")

    def tela_excluir_item(self, item):
        self.limpar_main()
        self.current_screen = self.tela_excluir_item
        self.current_args = (item,)

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Excluir Item").pack(pady=16)

        tk.Label(self.main_frame, text=f"Tem certeza que deseja excluir:\n\n{item}",
                 bg=self.colors["bg"], fg=self.colors["fg"], font=("Arial", 12)).pack(pady=14)

        tk.Button(self.main_frame, text="Confirmar Exclusão", command=lambda: self._confirm_excluir(item),
                  bg="#e53935", fg="#fff", bd=0, width=22, height=2).pack(pady=8)
        self.botao("Cancelar", lambda: self.go_to(self.tela_lista)).pack(pady=8)

    def _confirm_excluir(self, item):
        try:
            self.itens.remove(item)
            messagebox.showinfo("Sucesso", "Item excluído!")
            self.go_to(self.tela_lista)
        except ValueError:
            messagebox.showerror("Erro", "Item não encontrado!")

    def tela_perfil(self):
        self.limpar_main()
        self.current_screen = self.tela_perfil
        self.current_args = ()

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Perfil").pack(pady=14)

        # perfil básico com opção de editar nome
        tk.Label(self.main_frame, text=f"Nome: {self.usuario}", bg=self.colors["bg"], fg=self.colors["fg"],
                 font=("Arial", 12)).pack(pady=6)
        tk.Label(self.main_frame, text=f"Email: {self.profile_email}", bg=self.colors["bg"], fg=self.colors["fg"],
                 font=("Arial", 11)).pack(pady=6)

        self.botao("Editar Nome", self._editar_nome_dialog).pack(pady=8)
        self.botao("Voltar", lambda: self.go_to(self.tela_home)).pack(pady=10)

    def _editar_nome_dialog(self):
        # simples prompt
        novo = tk.simpledialog.askstring("Editar Nome", "Novo nome:", parent=self.root)
        if novo:
            self.usuario = novo
            messagebox.showinfo("Sucesso", "Nome atualizado!")
            self.go_to(self.tela_perfil)

    def tela_configuracoes(self):
        self.limpar_main()
        self.current_screen = self.tela_configuracoes
        self.current_args = ()

        self.main_frame.configure(bg=self.colors["bg"])
        self.titulo("Configurações").pack(pady=14)

        # modo escuro: mostra o estado atual e botões para alternar
        tk.Label(self.main_frame, text=f"Tema atual: {self.theme_name}", bg=self.colors["bg"], fg=self.colors["fg"],
                 font=("Arial", 11)).pack(pady=6)

        # botoes para alternar tema
        tk.Button(self.main_frame, text="Ativar Modo Escuro", command=lambda: self.set_theme("dark"),
                  bg=self.colors["button_bg"], fg=self.colors["button_fg"], bd=0, width=22, height=2).pack(pady=6)
        tk.Button(self.main_frame, text="Ativar Modo Claro", command=lambda: self.set_theme("light"),
                  bg=self.colors["button_bg"], fg=self.colors["button_fg"], bd=0, width=22, height=2).pack(pady=6)

        # outras configurações fictícias
        tk.Label(self.main_frame, text="Outras Opções", bg=self.colors["bg"], fg=self.colors["fg"],
                 font=("Arial", 12, "bold")).pack(pady=10)
        tk.Label(self.main_frame, text="(Nenhuma disponível no momento)", bg=self.colors["bg"], fg=self.colors["fg"]).pack()

        self.botao("Voltar", lambda: self.go_to(self.tela_home)).pack(pady=12)

# ---------------- Inicializa a aplicação ----------------
if __name__ == "__main__":
    root = tk.Tk()
    app = App(root)
    root.mainloop()
