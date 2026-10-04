Entendido. Se você quer **apenas** arquivos `.bat` (CMD) e `.ps1` (PowerShell), a abordagem muda: o Visual Basic Script (VBS) é o que permite criar janelas bonitas, mas como você pediu só BAT/PS1, vamos usar o **PowerShell** para renderizar uma interface gráfica (GUI) moderna dentro do terminal ou como uma janela flutuante, e o **BAT** para controlar o sistema.

Abaixo está um exemplo de **"App de Lista de Tarefas"** que salva os dados no seu computador usando apenas esses dois formatos.

### Como funciona:
1.  O arquivo `.ps1` cria a janela visual (botões, campos de texto).
2.  Ele salva os dados em um arquivo de texto simples (`.txt`) na mesma pasta.
3.  Você pode abrir esse arquivo `.ps1` direto no Windows.

---

### 1. Arquivo Principal: `MeuApp.ps1`
Este é o "código" da interface. Copie e cole no Bloco de Notas.

```powershell
# MeuApp.ps1 - Interface Gráfica Simples em PowerShell
Add-Type -AssemblyName System.Windows.Forms
Add-Type -AssemblyName System.Drawing

# Janela Principal
$form = New-Object System.Windows.Forms.Form
$form.Text = "Minha Lista de Tarefas"
$form.Size = New-Object System.Drawing.Size(400, 300)
$form.StartPosition = "CenterScreen"

# Campo de Texto
$inputBox = New-Object System.Windows.Forms.TextBox
$inputBox.Location = New-Object System.Drawing.Point(10, 20)
$inputBox.Size = New-Object System.Drawing.Size(280, 20)
$form.Controls.Add($inputBox)

# Botão Adicionar
$btnAdd = New-Object System.Windows.Forms.Button
$btnAdd.Location = New-Object System.Drawing.Point(300, 15)
$btnAdd.Size = New-Object System.Drawing.Size(80, 25)
$btnAdd.Text = "Salvar"
$btnAdd.Add_Click({
    $task = $inputBox.Text
    if ($task -ne "") {
        # Salva no arquivo de texto (o app fica salvo no PC aqui)
        "$task | $(Get-Date)" | Add-Content -Path "tarefas.txt"
        $inputBox.Clear()
        RefreshList
        [System.Windows.Forms.MessageBox]::Show("Salvo com sucesso!")
    }
})
$form.Controls.Add($btnAdd)

# Lista de Tarefas (ListBox)
$listBox = New-Object System.Windows.Forms.ListBox
$listBox.Location = New-Object System.Drawing.Point(10, 60)
$listBox.Size = New-Object System.Drawing.Size(370, 180)
$form.Controls.Add($listBox)

function RefreshList {
    $listBox.Items.Clear()
    if (Test-Path "tarefas.txt") {
        Get-Content "tarefas.txt" | ForEach-Object {
            $listBox.Items.Add($_)
        }
    }
}

# Carregar tarefas ao iniciar
RefreshList

# Mostrar a janela
$form.ShowDialog()
```

---

### 2. Arquivo Auxiliar: `iniciar.bat`
Crie um arquivo chamado `iniciar.bat` na mesma pasta para facilitar a abertura sem precisar clicar duas vezes no .ps1 (que às vezes pede permissão).

```batch
@echo off
REM Inicia o aplicativo PowerShell
powershell.exe -ExecutionPolicy Bypass -File "%~dp0MeuApp.ps1"
pause
```

### Como salvar no computador:
1.  Crie uma pasta nova no seu Desktop chamada "MeuApp".
2.  Dentro dela, crie o arquivo `MeuApp.ps1` com o primeiro código.
3.  Crie o arquivo `iniciar.bat` com o segundo código.
4.  Quando você abrir o `.bat`, ele vai rodar o `.ps1`.
5.  Tudo que você digitar será salvo automaticamente no arquivo `tarefas.txt` dentro dessa pasta. Mesmo se fechar o app, os dados continuam lá.

Isso é o mais próximo de um "app salvo" usando apenas as ferramentas nativas do Windows (BAT/PS1) sem precisar instalar nada extra.
