# 🔥 Hardening de Rede: Configuração UFW para Ambiente de Pentest

## 1. Verificação de Proxy (Prevenção de Erros)

O primeiro passo em qualquer nova instalação de ambiente de teste é garantir que nenhum proxy esteja ativo para evitar conflitos de roteamento em redes privadas (como túneis VPN), resolvendo problemas como o do AnonSurf no Parrot OS.

* **Ação:** Acessar o NetworkManager via terminal para inspecionar e definir a configuração de Proxy.
    ```bash
    nm-connection-editor
    ```
* **Configuração:** Garantir que a opção **Network Proxy** esteja definida como **"None"** ou **"Direct internet connection"**.

## 2. Configuração de Firewall com UFW

O **UFW (Uncomplicated Firewall)** foi instalado e configurado para atuar como uma camada de proteção persistente, negando o tráfego de entrada desnecessário, mas permitindo o fluxo de dados da VPN.

### 2.1. Políticas Padrão

As políticas globais definem o comportamento do firewall para o tráfego não explicitamente permitido ou negado, priorizando a segurança contra ataques externos.

* **Bloquear Tráfego de Entrada:**
    ```bash
    sudo ufw default deny incoming
    ```
* **Permitir Tráfego de Saída:**
    ```bash
    sudo ufw default allow outgoing
    ```

### 2.2. Regras Essenciais e VPN

Foram adicionadas regras explícitas para permitir serviços de gerenciamento (SSH) e, crucialmente, todo o tráfego através da interface da VPN (`tun0`).

* **Permitir SSH (Gerenciamento):**
    ```bash
    sudo ufw allow 22/tcp
    ```
* **Permitir Tráfego da VPN (Entrada e Saída):**
    Essas regras garantem que o tráfego de ataque (saída) e as respostas do alvo (entrada) fluam livremente através do túnel.
    ```bash
    sudo ufw allow in on tun0
    sudo ufw allow out on tun0
    ```

### 2.3. Ativação e Verificação

Após definir as regras, o UFW foi ativado e seu status foi verificado para confirmar que as políticas estavam sendo aplicadas corretamente.

* **Ativar o UFW:**
    ```bash
    sudo ufw enable
    ```
* **Verificar o Status:**
    ```bash
    sudo ufw status verbose
    ```

## 3. Conclusão de Segurança

A configuração final do UFW garante que:
1.  O host (Kali Linux) esteja protegido contra conexões de entrada não solicitadas (política `deny incoming`).
2.  A funcionalidade de **Pentest** seja preservada, com o túnel VPN (`tun0`) livre para enviar e receber dados, resolvendo os problemas de conectividade anteriores.
