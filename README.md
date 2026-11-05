# Desafio DIO: Auditoria de Segurança com Kali Linux e Medusa

Este repositório documenta a execução prática de testes de intrusão simulados utilizando **Kali Linux** e a ferramenta **Medusa** contra a máquina alvo vulnerável **Metasploitable 2**. O objetivo principal é exercitar técnicas de varredura, enumeração, força bruta em múltiplos serviços e propor medidas de mitigação adequadas.

---

## 🛠️ 1. Configuração do Ambiente Controlado

O ambiente foi montado utilizando o **Oracle VirtualBox**, isolando os sistemas em uma rede interna/host-only para garantir a segurança dos testes.

### Instalação e Inicialização das Máquinas
*   **Máquina de Ataque:** Kali Linux
*   **Máquina Alvo:** Metasploitable 2

![Kali Instalado](1-%20Kali%20instalado.png)
*Figura 1: Instalação do ambiente Kali Linux.*

<img width="1365" height="765" alt="2 - Meta instalado" src="https://github.com/user-attachments/assets/ce7079c0-bb42-4290-8a78-e919b6972ba4" />

*Figura 2: Inicialização e configuração da máquina Metasploitable 2.*

![Meta Iniciado](3-%20meta%20iniciado.png)
*Figura 3: Sistema alvo pronto para os testes.*

### Snapshot de Segurança
Foi criado um ponto de restauração (*Snapshot*) chamado `configuracaoinicial` na máquina alvo para garantir a integridade do laboratório antes dos ataques.

![Snapshot Criado](4-%20Snapshot%20criado.png)
*Figura 4: Criação do Snapshot de segurança no VirtualBox.*

### Identificação do IP Alvo
Através do comando `ip a` executado diretamente no terminal do Metasploitable, identificou-se o endereço IP fixado na interface `eth0`: **`192.168.56.101`**.

![IP Meta](5-%20Ip%20Meta.png)
*Figura 5: Identificação do IP da máquina vulnerável.*

---

## 🔍 2. Reconhecimento Avançado e Enumeração

Para mapear a superfície de ataque e descobrir quais serviços e informações estavam expostos no alvo, realizaram-se varreduras a partir do terminal do Kali Linux.

### Varredura de Portas e Serviços (Nmap)
Realizou-se a verificação de portas abertas para entender quais serviços estavam ativos.

![Comando Nmap](6-%20Comando%20para%20verificar%20port....png)
*Figura 6: Execução do comando de varredura.*

![Portas Abertas](7-%20Portas%20abertas%20do%20FTP.png)
*Figura 7: Identificação das portas abertas e serviços (FTP, HTTP, SMB).*

---

## 📋 3. Criação de Dicionários Personalizados (Wordlists)

Utilizando comandos estruturados de terminal, foram criados arquivos de texto contendo combinações comuns de usuários e senhas para alimentar os testes automatizados.

![Lista de Usuários e Senhas](9-%20Lista%20de%20usuarios%20e%20senhas%20cri....png)
*Figura 8: Estruturação das wordlists personalizadas para o ataque.*

---

## ⚔️ 4. Execução dos Ataques de Força Bruta e Resultados

### Cenário A: Força Bruta e Validação via FTP
O Medusa foi configurado para testar combinações contra a porta 21 (FTP).

![Sucesso no Ataque Medusa FTP](10-%20Sucesso%20no%20ataque%20-%20medus....png)
*Figura 9: Medusa localizando as credenciais válidas do FTP.*

![Conectado ao FTP](8-%20Conectado%20ao%20FTP.png)
*Figura 10: Tentativa de login e estabelecimento de conexão.*

![Login com Sucesso FTP](11-%20login%20com%20sucesso%20no%20ftp%20in....png)
*Figura 11: Validação manual de acesso ao servidor FTP.*

### Cenário B: Auditoria de Credenciais no SMB (Samba) e Cruzamento de Informações
Para simular um ataque focado na infraestrutura de rede local e compartilhamentos, utilizamos comandos de varredura e o módulo do Medusa para quebrar as credenciais do protocolo SMB.

![Sucesso ao Cruzar Informações](12-%20Sucesso%20ao%20cruzar%20informa%C3%A7....png)
*Figura 12: Mapeamento e validação de informações do sistema alvo.*

![Comando do Ataque SMB](14-%20comando%20do%20ataque%202.png)
*Figura 13: Execução do comando Medusa direcionado ao serviço SMB.*

![Sucesso no Ataque Medusa SMB](13-%20sucesso%20no%20ataque%20-%20medus....png)
*Figura 14: Credencial administrativa válida identificada no SMB.*

### Cenário C: Automação Web via HTTP (Formulário DVWA)
Para testar a segurança do portal web do Damn Vulnerable Web Application (DVWA), executou-se a simulação de password spraying e força bruta corporativa.

![Simulando Ataque Corporativo](15-%20Simulando%20ataque%20corporat....png)
*Figura 15: Configuração do ataque contra o formulário de login HTTP.*

![Simulando Ataque de Senhas](16-%20Simulando%20ataque%20de%20senh....png)
*Figura 16: Automação massiva de tentativas de autenticação web.*

![Ataque Bem Sucedido](17-%20Ataque%20bm%20sucedido%20-%20senh....png)
*Figura 17: Credenciais do DVWA quebradas com sucesso.*

---

## 🛡️ 5. Recomendações de Mitigação e Defesa

Com base nas vulnerabilidades e facilidades encontradas durante as simulações, as seguintes contramedidas de segurança são recomendadas:

1.  **Políticas Rígidas de Credenciais:** Desativar completamente usuários padrões de fábrica (como `msfadmin`), remover credenciais fracas pré-configuradas e impor políticas que exijam senhas longas e complexas.
2.  **Bloqueio de Contas (Account Lockout):** Configurar mecanismos no sistema operacional, serviços de rede (SSH, FTP, SMB) e aplicações web que suspendam temporariamente o login de usuários após sucessivas tentativas incorretas, inviabilizando ataques automatizados como os do Medusa.
3.  **Implementação de Mecanismos de Proteção Ativa:** Adotar soluções de monitoramento e detecção como o **Fail2ban** para analisar logs de autenticação em tempo real e banir temporariamente no Firewall do servidor os endereços IP que demonstrem padrões de varreduras agressivas ou ataques de força bruta.
