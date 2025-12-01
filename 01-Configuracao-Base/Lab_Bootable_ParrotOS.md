# 🐧 Lab 01: Criação de Mídia Bootável do Parrot OS (Resolução de Problemas de File System)

Este laboratório documenta o processo de criação de uma mídia USB bootável para o sistema operacional **Parrot Security OS** e as etapas de solução de problemas encontradas durante a instalação inicial. O objetivo principal era validar o processo de boot de um sistema operacional de segurança em hardware real.

## 1. Ferramentas e Recursos Utilizados

* **Sistema Operacional:** Parrot Security OS (ISO `Parrot-security-6.4_amd64.iso`)
* **Hardware:** Notebook Samsung (Modelo E40)
* **Mídia:** Pendrive USB 16GB
* **Ferramentas de Criação:**
    * **Rufus:** Primeira tentativa (falha de compatibilidade com o hardware Samsung).
    * **Ventoy:** Solução de sucesso para o processo de boot ().

## 2. Processo Inicial e Falhas Encontradas

### 🔴 Tentativa 1: Problema de Compatibilidade (Rufus)
A criação inicial da mídia utilizando o Rufus falhou ao tentar bootar no hardware Samsung. A falha indicou um problema de compatibilidade na forma como o Rufus gravou a ISO versus as configurações de firmware (UEFI/BIOS) do notebook.

### 🔴 Tentativa 2: Erro de Sistema de Arquivos (Ventoy - FAT32)
Ao mudar para o Ventoy, o primeiro erro encontrado foi relacionado ao sistema de arquivos do pendrive:
* **Erro:** O sistema de arquivos **FAT32** não aceita arquivos únicos maiores que **4GB**. A ISO do Parrot OS ultrapassava este limite.
* **Ação de Correção:** O pendrive foi **reformatado** para **ExFAT**.

## 3. Solução de Sucesso (Ventoy + ExFAT)

A combinação da ferramenta **Ventoy** com o sistema de arquivos **ExFAT** resolveu ambos os problemas:

* **Ventoy:** Permitiu carregar a ISO de forma mais flexível, contornando a incompatibilidade do hardware.
* **ExFAT:** Suportou o tamanho do arquivo ISO (> 4GB), permitindo que a imagem fosse copiada corretamente para o pendrive.

### Etapas Finais (Boot e Execução)

1.  O Ventoy foi inicializado e a ISO do Parrot OS foi selecionada ().
2.  O menu de boot do Parrot OS foi carregado com sucesso ().
3.  A opção **"Try / Install"** foi selecionada, e o sistema carregou o desktop do Parrot OS em modo Live ().

## 4. Lições Aprendidas

* **Flexibilidade de Ferramentas:** Em ambientes heterogêneos (diferentes hardwares), é essencial ter alternativas como o Ventoy para garantir o sucesso do *boot*.
* **Limitações do File System:** A limitação de **4GB** do **FAT32** é um ponto crítico a ser sempre verificado ao trabalhar com ISOs de sistemas operacionais modernos.
* **Modo Live vs. Persistente:** Este laboratório foi um **teste de habilidade de boot**. Os próximos passos incluirão a criação de VMs persistentes para configurar **firewalls** e outros serviços de segurança.
* 
