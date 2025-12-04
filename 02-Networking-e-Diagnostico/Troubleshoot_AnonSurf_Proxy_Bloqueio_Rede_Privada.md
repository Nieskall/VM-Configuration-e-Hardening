# ⚙️ Solução de Conectividade: O Proxy AnonSurf e a Rede Privada

Este documento detalha a resolução de um problema de conectividade de Camada 7 (HTTP) que persistiu por quatro dias, apesar da conectividade de Camada 3 (IP) estar funcionando corretamente. A causa raiz era uma **configuração de proxy** específica do **Parrot OS**.

## 1. O Problema e o Contexto (Sintomas Contraditórios)

* **Contexto:** VM Parrot OS conectada a uma rede privada/VPN, tentando acessar o alvo (`10.10.10.245`).
* **Sintoma 1 (Sucesso na Camada 3 - IP):** O comando `ping 10.10.10.245` **FUNCIONAVA**.
    * **Conclusão:** O roteamento principal (VPN) estava correto.
* **Sintoma 2 (Falha na Camada 7 - HTTP):** O navegador (Firefox) falhava ao acessar `http://10.10.10.245`, retornando "Unable to connect".
    * **Conclusão:** O problema estava acima da Camada IP (4 ou 7).

## 2. Diagnóstico Metódico (Causas Descartadas)

O problema foi rastreado descartando metodicamente as causas mais comuns:

| Causa Tentada | Comando/Verificação | Resultado | Conclusão |
| :--- | :--- | :--- | :--- |
| **Firewall Local** | `sudo iptables -L` | Políticas INPUT/OUTPUT eram **ACCEPT**. | Não era bloqueio local. |
| **Cache DNS** | Tentativa de `systemd-resolve --flush-caches` | Comando `not found`. | Não havia cache DNS ativo. |
| **Serviço Alvo** | `nmap -Pn -p 80,443,21,22 10.10.10.245` | **Porta 80 estava OPEN**. | Não era inatividade do serviço alvo. |

## 3. Causa Raiz: O AnonSurf Proxy

O ponto de falha foi encontrado na configuração de rede do sistema, um detalhe específico da distribuição **Parrot OS**.

* **Verificação:** Análise de **"Network Proxy Preferences"** do sistema.
* **A Descoberta:** A opção **"Automatic proxy configuration"** estava ativada, apontando para o arquivo de configuração: `/etc/anonsurf/onion.pac`.
* **Análise de Falha:** O AnonSurf é uma ferramenta do Parrot OS projetada para forçar o tráfego de navegação (HTTP/Camada 7) através da rede **Tor**. A rede Tor, por projeto, **rejeita ativamente** o tráfego destinado a endereços de redes privadas (RFC 1918), como `10.10.10.245`. Isso interceptava e bloqueava silenciosamente todas as tentativas de conexão HTTP, mesmo que o `ping` (que é Camada 3 e não usa proxy HTTP) funcionasse.

## 4. Solução Implementada

O bloqueio foi removido desativando o serviço que forçava o tráfego através da rede Tor.

* **Ação Principal:** O comando para parar o serviço AnonSurf foi executado.

    ```bash
    sudo anonsurf stop
    ```

* **Resultado Final:** A configuração de rede foi revertida para o modo **"Direct internet connection"**, e o acesso ao alvo (`http://10.10.10.245`) foi **restabelecido com sucesso**.

---

### 💡 Lição de Segurança e Administração de Sistemas

**O mais valioso:** **Confiança e Contexto de Software.**

* **Regra de Diagnóstico:** Se o PING funcionar, mas o NAVEGADOR falhar, o problema está quase sempre nas Camadas 4 (Firewall/Portas) ou 7 (**Proxy/Cache/Aplicação**).
* **Consciência do SO:** Ferramentas de *Hardening* ou anonimato (como **AnonSurf** no Parrot) alteram fundamentalmente o comportamento da rede em nível de aplicação (Proxy) e podem ser a causa de falhas em redes privadas/VPNs.

Esta documentação agora serve como um guia de *troubleshooting* avançado, cobrindo erros que não são corrigidos por comandos básicos.
