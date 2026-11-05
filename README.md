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

<img width="1362" height="764" alt="3 - meta iniciado" src="https://github.com/user-attachments/assets/7c20d1a4-08c8-4099-a2b7-61f76b3492e6" />

*Figura 3: Sistema alvo pronto para os testes.*

### Snapshot de Segurança
Foi criado um ponto de restauração (*Snapshot*) chamado `configuracaoinicial` na máquina alvo para garantir a integridade do laboratório antes dos ataques.

<img width="721" height="474" alt="4 - Snapshot criado" src="https://github.com/user-attachments/assets/a413a172-3b26-4871-af22-087f9109ea22" />

*Figura 4: Criação do Snapshot de segurança no VirtualBox.*

### Identificação do IP Alvo
Através do comando `ip a` executado diretamente no terminal do Metasploitable, identificou-se o endereço IP fixado na interface `eth0`: **`192.168.56.101`**.

<img width="983" height="413" alt="5 - Ip Meta" src="https://github.com/user-attachments/assets/ee92ef10-7532-4ab8-856c-1f9fefed1ed6" />

*Figura 5: Identificação do IP da máquina vulnerável.*

---

## 🔍 2. Reconhecimento Avançado e Enumeração

Para mapear a superfície de ataque e descobrir quais serviços e informações estavam expostos no alvo, realizaram-se varreduras a partir do terminal do Kali Linux.

### Varredura de Portas e Serviços (Nmap)
Realizou-se a verificação de portas abertas para entender quais serviços estavam ativos.

<img width="1365" height="760" alt="6 - Comando para verificar portas abertas do FTP" src="https://github.com/user-attachments/assets/28d4ba00-1dc5-4a7a-8e36-0cc301f1a1e1" />

*Figura 6: Execução do comando de varredura.*

<img width="1365" height="767" alt="7 - Portas abertas do FTP" src="https://github.com/user-attachments/assets/aced7b4a-fcd5-4d23-9d8f-3c22aab2851d" />

*Figura 7: Identificação das portas abertas e serviços (FTP, HTTP, SMB).*

---

## 📋 3. Criação de Dicionários Personalizados (Wordlists)

Utilizando comandos estruturados de terminal, foram criados arquivos de texto contendo combinações comuns de usuários e senhas para alimentar os testes automatizados.

<img width="1365" height="767" alt="8 - Conectado ao FTP" src="https://github.com/user-attachments/assets/28acd20c-ffb9-417e-b204-5ee9ac609160" />

*Figura 8: Estruturação das wordlists personalizadas para o ataque.*

---

## ⚔️ 4. Execução dos Ataques de Força Bruta e Resultados

### Cenário A: Força Bruta e Validação via FTP
O Medusa foi configurado para testar combinações contra a porta 21 (FTP).

<img width="1365" height="766" alt="9 - Lista de usuarios e senhas criadas" src="https://github.com/user-attachments/assets/920ea5ed-eed6-4e0c-89fb-1ca7fd759174" />

*Figura 9: Medusa localizando as credenciais válidas do FTP.*

<img width="1365" height="757" alt="10 - Sucesso no ataque - medusa" src="https://github.com/user-attachments/assets/c5a908ba-0baf-4805-8ded-98c6777f636c" />

*Figura 10: Tentativa de login e estabelecimento de conexão.*

<img width="1360" height="765" alt="11- login com sucesso no ftp invadido" src="https://github.com/user-attachments/assets/6e5231e8-5a04-4eb0-9514-277cc6e032a0" />

*Figura 11: Validação manual de acesso ao servidor FTP.*

### Cenário B: Auditoria de Credenciais no SMB (Samba) e Cruzamento de Informações
Para simular um ataque focado na infraestrutura de rede local e compartilhamentos, utilizamos comandos de varredura e o módulo do Medusa para quebrar as credenciais do protocolo SMB.

<img width="1354" height="730" alt="12 - Sucesso ao cruzar informações de login e senha" src="https://github.com/user-attachments/assets/b22ee5e8-dca6-4a58-934d-cbc0606b435f" />

*Figura 12: Mapeamento e validação de informações do sistema alvo.*

<img width="1361" height="682" alt="13 - sucesso no ataque  - medusa 2" src="https://github.com/user-attachments/assets/216785ad-bf7d-48f6-af01-b69abb1ac49f" />

*Figura 13: Execução do comando Medusa direcionado ao serviço SMB.*

<img width="1352" height="728" alt="14 - comando do ataque 2" src="https://github.com/user-attachments/assets/2d4568b2-0cd0-43b3-aae8-6f0e2ba8fc23" />

*Figura 14: Credencial administrativa válida identificada no SMB.*

### Cenário C: Automação Web via HTTP (Formulário DVWA)
Para testar a segurança do portal web do Damn Vulnerable Web Application (DVWA), executou-se a simulação de password spraying e força bruta corporativa.

<img width="1362" height="724" alt="15 - Simulando ataque corporativo" src="https://github.com/user-attachments/assets/17bea34e-ed0f-4298-bd5c-a61b1240f656" />

*Figura 15: Configuração do ataque contra o formulário de login HTTP.*

<img width="1361" height="761" alt="16 - Simulando ataque de senhas vulneráveis" src="https://github.com/user-attachments/assets/89f513aa-6e5e-4fd6-86d9-bcd2b2caaaad" />

*Figura 16: Automação massiva de tentativas de autenticação web.*

<img width="1356" height="760" alt="17 - Ataque bm sucecido - senha descoberta" src="https://github.com/user-attachments/assets/8de27ff3-2e57-4ea1-bccb-8cbaec24146c" />

*Figura 17: Credenciais do DVWA quebradas com sucesso.*

---

## 🛡️ 5. Recomendações de Mitigação e Defesa

Com base nas vulnerabilidades e facilidades encontradas durante as simulações, as seguintes contramedidas de segurança são recomendadas:

1.  **Políticas Rígidas de Credenciais:** Desativar completamente usuários padrões de fábrica (como `msfadmin`), remover credenciais fracas pré-configuradas e impor políticas que exijam senhas longas e complexas.
2.  **Bloqueio de Contas (Account Lockout):** Configurar mecanismos no sistema operacional, serviços de rede (SSH, FTP, SMB) e aplicações web que suspendam temporariamente o login de usuários após sucessivas tentativas incorretas, inviabilizando ataques automatizados como os do Medusa.
3.  **Implementação de Mecanismos de Proteção Ativa:** Adotar soluções de monitoramento e detecção como o **Fail2ban** para analisar logs de autenticação em tempo real e banir temporariamente no Firewall do servidor os endereços IP que demonstrem padrões de varreduras agressivas ou ataques de força bruta.
