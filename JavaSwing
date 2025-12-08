mport com.formdev.flatlaf.FlatDarkLaf;
import com.formdev.flatlaf.FlatLightLaf;

import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

public class ProjectSeminario {

    private JFrame frame;
    private JPanel mainPanel;
    private CardLayout cardLayout;

    private java.util.List<String> itens;
    private String usuario;
    private String profileEmail;

    private String themeName = "light";
    private Map<String, Color> colors = new HashMap<>();

    private JTextField loginUserField;
    private JPasswordField loginPassField;

    private Map<String, JPanel> screens = new HashMap<>();
    private String currentSelectedItem = null;

    public ProjectSeminario() {
        itens = new ArrayList<>();
        itens.add("Item 1");
        itens.add("Item 2");
        itens.add("Item 3");

        usuario = "Aluno Exemplo";
        profileEmail = "email@exemplo.com";

        setColorsForTheme();

        SwingUtilities.invokeLater(() -> {
            initLookAndFeel();
            createAndShowGUI();
            showLoadingThen("login", 700, true, null);
        });
    }

    private void initLookAndFeel() {
        try {
            if ("dark".equals(themeName)) {
                FlatDarkLaf.setup();
            } else {
                FlatLightLaf.setup();
            }
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    private void setColorsForTheme() {
        if ("dark".equals(themeName)) {
            colors.put("bg", new Color(10,10,10));
            colors.put("fg", Color.WHITE);
            colors.put("card", new Color(20,20,20));
            colors.put("button_bg", new Color(40,40,40));
            colors.put("button_fg", Color.WHITE);
        } else {
            colors.put("bg", new Color(240,240,240));
            colors.put("fg", Color.BLACK);
            colors.put("card", Color.WHITE);
            colors.put("button_bg", new Color(60,120,200));
            colors.put("button_fg", Color.WHITE);
        }
    }

    private void createAndShowGUI() {
        frame = new JFrame("Projeto Seminário");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(380, 560);
        frame.setResizable(false);
        frame.setLocationRelativeTo(null);

        cardLayout = new CardLayout();
        mainPanel = new JPanel(cardLayout);
        mainPanel.setBorder(new EmptyBorder(8,8,8,8));
        mainPanel.setBackground(colors.get("bg"));

        screens.put("login", buildLoginScreen());
        screens.put("home", buildHomeScreen());
        screens.put("lista", buildListaScreen());
        screens.put("detalhes", buildDetalhesScreen());
        screens.put("adicionar", buildAdicionarScreen());
        screens.put("editar", buildEditarScreen());
        screens.put("excluir", buildExcluirScreen());
        screens.put("perfil", buildPerfilScreen());
        screens.put("configuracoes", buildConfiguracoesScreen());

        for (Map.Entry<String, JPanel> e : screens.entrySet()) {
            mainPanel.add(e.getValue(), e.getKey());
        }

        frame.add(mainPanel);
        frame.setVisible(true);
    }

    private void showLoadingThen(String screen, int delayMs, boolean firstLoad, Runnable after) {
        final JDialog loading = new JDialog(frame);
        loading.setUndecorated(true);
        loading.setSize(220, 100);
        loading.setModal(false);

        JPanel p = new JPanel(new BorderLayout());
        p.setBorder(new EmptyBorder(10,10,10,10));
        p.setBackground(Color.WHITE);
        JLabel lbl = new JLabel("Carregando...", SwingConstants.CENTER);
        p.add(lbl);
        loading.add(p);

        loading.setLocationRelativeTo(frame);
        loading.setVisible(true);

        int totalDelay = firstLoad ? delayMs + 300 : delayMs;
        new Timer(totalDelay, e -> {
            loading.dispose();
            showScreen(screen);
            if (after != null) after.run();
            ((Timer)e.getSource()).stop();
        }).start();
    }

    private void goTo(String screen) {
        showLoadingThen(screen, 500, false, null);
    }

    private void showScreen(String name) {

        switch (name) {
            case "lista" -> refreshListaScreen();
            case "perfil" -> refreshPerfilScreen();
            case "detalhes" -> refreshDetalhesScreen(currentSelectedItem);
            case "editar" -> refreshEditarScreen(currentSelectedItem);
            case "excluir" -> refreshExcluirScreen(currentSelectedItem);
            case "configuracoes" -> refreshConfiguracoesScreen();
        }

        cardLayout.show(mainPanel, name);
    }

    private JButton makeButton(String text, ActionListener action) {
        JButton btn = new JButton(text);
        btn.setFocusPainted(false);
        btn.addActionListener(action);
        btn.setPreferredSize(new Dimension(200, 36));
        return btn;
    }

    // ------------------------- TELAS -------------------------------

    private JPanel buildLoginScreen() {
        JPanel p = new JPanel();
        p.setLayout(new BoxLayout(p, BoxLayout.Y_AXIS));
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Tela de Login");
        title.setFont(new Font("Arial", Font.BOLD, 20));
        title.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(title);

        loginUserField = new JTextField();
        loginUserField.setMaximumSize(new Dimension(260, 28));
        p.add(centeredComponent(loginUserField));

        loginPassField = new JPasswordField();
        loginPassField.setMaximumSize(new Dimension(260, 28));
        p.add(centeredComponent(loginPassField));

        p.add(centeredComponent(makeButton("Entrar", evt -> doLoginAction())));

        return p;
    }

    private void doLoginAction() {
        String u = loginUserField.getText().trim();
        String s = new String(loginPassField.getPassword()).trim();

        if (u.isEmpty() || s.isEmpty()) {
            JOptionPane.showMessageDialog(frame, "Preencha os campos!");
            return;
        }

        usuario = u;
        goTo("home");
    }

    private JPanel buildHomeScreen() {
        JPanel p = new JPanel();
        p.setLayout(new BoxLayout(p, BoxLayout.Y_AXIS));
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Tela Home", SwingConstants.CENTER);
        title.setFont(new Font("Arial", Font.BOLD, 20));
        title.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(title);

        JLabel welcome = new JLabel("Olá, " + usuario + "!");
        welcome.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(welcome);

        p.add(centeredComponent(makeButton("Lista de Itens", e -> goTo("lista"))));
        p.add(centeredComponent(makeButton("Adicionar Item", e -> goTo("adicionar"))));
        p.add(centeredComponent(makeButton("Perfil", e -> goTo("perfil"))));
        p.add(centeredComponent(makeButton("Configurações", e -> goTo("configuracoes"))));

        p.add(centeredComponent(makeButton("Sair", e -> showLoadingThen("login", 300, false, null))));

        return p;
    }

    private JPanel buildListaScreen() {
        JPanel p = new JPanel(new BorderLayout());
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Lista de Itens", SwingConstants.CENTER);
        title.setFont(new Font("Arial", Font.BOLD, 18));
        p.add(title, BorderLayout.NORTH);

        JPanel center = new JPanel();
        center.setLayout(new BoxLayout(center, BoxLayout.Y_AXIS));
        center.setName("listaCenter");
        p.add(new JScrollPane(center), BorderLayout.CENTER);

        JButton back = new JButton("Voltar");
        back.addActionListener(e -> goTo("home"));
        p.add(back, BorderLayout.SOUTH);

        return p;
    }

    private void refreshListaScreen() {
        JPanel p = screens.get("lista");
        JScrollPane sp = (JScrollPane) p.getComponent(1);
        JPanel center = (JPanel) sp.getViewport().getView();
        center.removeAll();

        for (String item : itens) {
            JPanel card = new JPanel(new BorderLayout());
            card.setBorder(BorderFactory.createLineBorder(Color.GRAY));

            JLabel l = new JLabel(item);
            card.add(l, BorderLayout.WEST);

            JPanel actions = new JPanel();

            JButton abrir = new JButton("Abrir");
            abrir.addActionListener(e -> {
                currentSelectedItem = item;
                goTo("detalhes");
            });

            JButton editar = new JButton("Editar");
            editar.addActionListener(e -> {
                currentSelectedItem = item;
                goTo("editar");
            });

            JButton excluir = new JButton("Excluir");
            excluir.addActionListener(e -> {
                currentSelectedItem = item;
                goTo("excluir");
            });

            actions.add(abrir);
            actions.add(editar);
            actions.add(excluir);

            card.add(actions, BorderLayout.EAST);
            center.add(card);
        }

        center.revalidate();
        center.repaint();
    }

    private JPanel buildDetalhesScreen() {
        JPanel p = new JPanel();
        p.setLayout(new BoxLayout(p, BoxLayout.Y_AXIS));
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Detalhes do Item");
        title.setFont(new Font("Arial", Font.BOLD, 18));
        title.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(title);

        JLabel lbl = new JLabel();
        lbl.setName("detalheLabel");
        lbl.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(lbl);

        p.add(centeredComponent(makeButton("Voltar", e -> goTo("lista"))));

        return p;
    }

    private void refreshDetalhesScreen(String item) {
        JPanel p = screens.get("detalhes");
        JLabel lbl = (JLabel) findComponentByName(p, "detalheLabel");
        lbl.setText("Você abriu: " + item);
    }

    private JPanel buildAdicionarScreen() {
        JPanel p = new JPanel();
        p.setLayout(new BoxLayout(p, BoxLayout.Y_AXIS));
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Adicionar Item");
        title.setFont(new Font("Arial", Font.BOLD, 18));
        title.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(title);

        JTextField field = new JTextField();
        field.setMaximumSize(new Dimension(280, 28));
        field.setName("novoItemField");
        p.add(centeredComponent(field));

        p.add(centeredComponent(makeButton("Salvar", e -> {
            String txt = field.getText().trim();
            if (!txt.isEmpty()) {
                itens.add(txt);
                JOptionPane.showMessageDialog(frame, "Item adicionado!");
                field.setText("");
                goTo("lista");
            }
        })));

        p.add(centeredComponent(makeButton("Voltar", e -> goTo("home"))));

        return p;
    }

    private JPanel buildEditarScreen() {
        JPanel p = new JPanel();
        p.setLayout(new BoxLayout(p, BoxLayout.Y_AXIS));
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Editar Item");
        title.setFont(new Font("Arial", Font.BOLD, 18));
        title.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(title);

        JLabel atual = new JLabel();
        atual.setName("editar_atual_label");
        atual.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(atual);

        JTextField field = new JTextField();
        field.setMaximumSize(new Dimension(280,28));
        field.setName("editarField");
        p.add(centeredComponent(field));

        p.add(centeredComponent(makeButton("Salvar", e -> {
            String novo = field.getText().trim();
            if (!novo.isEmpty() && currentSelectedItem != null) {
                int idx = itens.indexOf(currentSelectedItem);
                if (idx >= 0) itens.set(idx, novo);
                goTo("lista");
            }
        })));

        p.add(centeredComponent(makeButton("Voltar", e -> goTo("detalhes"))));

        return p;
    }

    private void refreshEditarScreen(String item) {
        JPanel p = screens.get("editar");
        JLabel atual = (JLabel) findComponentByName(p, "editar_atual_label");
        JTextField field = (JTextField) findComponentByName(p, "editarField");

        atual.setText("Item atual: " + item);
        field.setText(item);
    }

    private JPanel buildExcluirScreen() {
        JPanel p = new JPanel();
        p.setLayout(new BoxLayout(p, BoxLayout.Y_AXIS));
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Excluir Item");
        title.setFont(new Font("Arial", Font.BOLD, 18));
        title.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(title);

        JLabel lbl = new JLabel();
        lbl.setName("confirmLabel");
        lbl.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(lbl);

        p.add(centeredComponent(makeButton("Confirmar Exclusão", e -> {
            itens.remove(currentSelectedItem);
            goTo("lista");
        })));

        p.add(centeredComponent(makeButton("Cancelar", e -> goTo("lista"))));

        return p;
    }

    private void refreshExcluirScreen(String item) {
        JPanel p = screens.get("excluir");
        JLabel lbl = (JLabel) findComponentByName(p, "confirmLabel");
        lbl.setText("Excluir: " + item + "?");
    }

    private JPanel buildPerfilScreen() {
        JPanel p = new JPanel();
        p.setLayout(new BoxLayout(p, BoxLayout.Y_AXIS));
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Perfil");
        title.setFont(new Font("Arial", Font.BOLD, 18));
        title.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(title);

        JLabel nome = new JLabel();
        nome.setName("perfil_nome");
        nome.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(nome);

        JLabel email = new JLabel();
        email.setName("perfil_email");
        email.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(email);

        p.add(centeredComponent(makeButton("Editar Nome", e -> editarNomeDialog())));
        p.add(centeredComponent(makeButton("Voltar", e -> goTo("home"))));

        return p;
    }

    private void refreshPerfilScreen() {
        JPanel p = screens.get("perfil");
        JLabel nome = (JLabel) findComponentByName(p, "perfil_nome");
        JLabel email = (JLabel) findComponentByName(p, "perfil_email");

        nome.setText("Nome: " + usuario);
        email.setText("Email: " + profileEmail);
    }

    private void editarNomeDialog() {
        String novo = JOptionPane.showInputDialog(frame, "Novo nome:", usuario);
        if (novo != null && !novo.trim().isEmpty()) {
            usuario = novo.trim();
            goTo("perfil");
        }
    }

    private JPanel buildConfiguracoesScreen() {
        JPanel p = new JPanel();
        p.setLayout(new BoxLayout(p, BoxLayout.Y_AXIS));
        p.setBackground(colors.get("bg"));

        JLabel title = new JLabel("Configurações");
        title.setFont(new Font("Arial", Font.BOLD, 18));
        title.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(title);

        JLabel tema = new JLabel("Tema atual: " + themeName);
        tema.setName("temaAtualLabel");
        tema.setAlignmentX(Component.CENTER_ALIGNMENT);
        p.add(tema);

        p.add(centeredComponent(makeButton("Modo Escuro", e -> setTheme("dark"))));
        p.add(centeredComponent(makeButton("Modo Claro", e -> setTheme("light"))));

        p.add(centeredComponent(makeButton("Voltar", e -> goTo("home"))));

        return p;
    }

    private void refreshConfiguracoesScreen() {
        JPanel p = screens.get("configuracoes");
        JLabel tema = (JLabel) findComponentByName(p, "temaAtualLabel");
        tema.setText("Tema atual: " + themeName);
    }
    private Component centeredComponent(Component c) {
        // se for um JComponent, chama setAlignmentX corretamente
        if (c instanceof JComponent) {
            ((JComponent) c).setAlignmentX(Component.CENTER_ALIGNMENT);
        }

        JPanel w = new JPanel();
        w.setOpaque(false);
        w.setLayout(new BoxLayout(w, BoxLayout.X_AXIS));
        w.add(Box.createHorizontalGlue());
        w.add(c);
        w.add(Box.createHorizontalGlue());
        return w;
    }

    private Component findComponentByName(Component parent, String name) {
        if (name.equals(parent.getName())) return parent;
        if (parent instanceof Container ct) {
            for (Component c : ct.getComponents()) {
                Component f = findComponentByName(c, name);
                if (f != null) return f;
            }
        }
        return null;
    }

    // FINALIZAÇÃO DO CÓDIGO
    private void setTheme(String newTheme) {
        themeName = newTheme;
        setColorsForTheme();
        initLookAndFeel();

        frame.dispose();
        createAndShowGUI();
        showScreen("configuracoes");
    }

    public static void main(String[] args) {
        new ProjectSeminario();
    }
}
