# Fundamentos sobre o ambiente Azure.

Bem-vindo ao meu repositório que visa consolidar meu aprendizado e experiência com o Microsoft Azure, focando em conceitos fundamentais, implantação e gerenciamento de recursos de computação. Aqui você encontrará resumos, anotações detalhadas e dicas práticas.

## Objetivo do Projeto

O objetivo principal deste repositório é:
*   **Documentar** conceitos-chave do Azure.
*   **Detalhar** processos de criação e gerenciamento de recursos.
*   **Compartilhar** dicas e melhores práticas.

## Tópicos Abordados

Este projeto foca inicialmente nos seguintes pilares do Azure:
1.  **Recursos do Azure:** Entendimento da hierarquia e organização.
2.  **Máquinas Virtuais (VMs):** Criação, configuração e planejamento.
3.  **Availability Sets:** Garantindo alta disponibilidade para VMs.
4.  **Virtual Machine Scale Sets (VMSS):** Gerenciamento e escalabilidade automatizada.
5.  **Azure Resource Manager (ARM) Templates:** Automação e implantação declarativa.

---

``` Markdown
# Recursos do Azure: Organização e Hierarquia

O Microsoft Azure é uma plataforma baseada na nuvem que fornece serviços de computação pela internet, oferecendo inovação rápida, recursos flexíveis e economia de escala. Para gerenciar e organizar esses serviços de forma eficiente, o Azure utiliza uma estrutura hierárquica.

## O que são Recursos do Azure?

Um **Recurso** é o bloco de construção fundamental no Azure. Qualquer item que você cria, provisiona ou implanta no Azure é considerado um recurso, como máquinas virtuais (VMs), redes virtuais, bancos de dados e serviços cognitivos.

## Grupos de Recursos

Os **Grupos de Recursos** são agrupamentos lógicos para seus recursos do Azure.

*   Ao criar um recurso, ele deve ser colocado em um grupo de recursos.
*   Um grupo de recursos pode conter muitos recursos, mas um único recurso pode estar em apenas um grupo de recursos por vez.
*   Ações aplicadas a um grupo de recursos afetam todos os recursos dentro dele (por exemplo, exclusão de um grupo de recursos apaga todos os recursos contidos).
*   Grupos de recursos não podem ser aninhados.
*   São úteis para organizar e gerenciar o ciclo de vida de aplicações ou ambientes (ex: ambiente de desenvolvimento temporário).

## Assinaturas do Azure

Uma **Assinatura do Azure** é uma unidade lógica de serviços do Azure vinculada a uma conta Azure. Ela serve como um limite de segurança e cobrança.

*   Uma assinatura fornece acesso autenticado e autorizado aos produtos e serviços do Azure.
*   Contas gratuitas e Pay-As-You-Go são opções para iniciar uma assinatura.
*   Uma conta gratuita inclui acesso limitado por 12 meses e créditos para os primeiros 30 dias.

## Grupos de Gerenciamento

Os **Grupos de Gerenciamento** fornecem um nível de escopo acima das assinaturas, permitindo gerenciar o acesso, políticas e conformidade em escala para múltiplas assinaturas.

*   As assinaturas herdam as condições aplicadas ao grupo de gerenciamento.
*   É possível suportar até 10.000 grupos de gerenciamento em um único diretório.
*   A árvore de grupos de gerenciamento pode ter até seis níveis de profundidade, sem incluir o nível raiz ou de assinatura.

## Tags de Recursos

**Tags** são elementos de metadados na forma de pares chave-valor que você aplica aos recursos do Azure para organizá-los logicamente.

*   Podem ser aplicadas a recursos, grupos de recursos e assinaturas (mas não a grupos de gerenciamento).
*   São muito úteis para acumular informações de cobrança, permitindo agrupar dados de uso por centro de custo ou ambiente de produção.
*   **Cuidado**: Tags são armazenadas como texto sem formatação; evite adicionar valores confidenciais.
*   Recursos não herdam tags de um grupo de recursos ou assinatura.

## Máquinas Virtuais (VMs) do Azure: Criação e Gerenciamento

As Máquinas Virtuais (VMs) do Azure são um dos tipos mais comuns de recursos de computação do tipo **Infrastructure as a Service (IaaS)**, oferecendo controle total sobre a configuração. Elas fornecem a flexibilidade da virtualização sem a necessidade de comprar e manter hardware físico.

## Planejamento de VMs

Antes de criar uma VM, é crucial considerar vários fatores:

### 1. Rede
*   A rede é a primeira consideração. As VMs precisam de redes virtuais (VNets) para comunicação privada com outros serviços Azure e redes locais.
*   Defina o espaço de endereço e pelo menos uma sub-rede para a VNet.
*   Evite sobreposição de espaços de endereço se for conectar a outras VNets ou redes locais.
*   **Subnets** segmentam a VNet para melhorar segurança, desempenho e gerenciamento. O Azure reserva cinco endereços IP em cada sub-rede.

### 2. Nome da VM
*   Escolha um nome significativo e consistente que identifique facilmente a função da VM (ex: `deveus-webvm01`). O nome define um recurso Azure gerenciável e não é fácil de ser alterado posteriormente.

### 3. Localização (Região)
*   Implante VMs o mais próximo possível dos seus usuários para reduzir a latência.
*   As regiões do Azure são áreas geográficas com um ou mais datacenters.
*   Considere a disponibilidade de hardware e serviços específicos, bem como requisitos de conformidade e obrigações legais.
*   Existem diferenças de preço entre os locais.

### 4. Tamanho da VM
*   O tamanho da VM determina a quantidade de CPU, memória e armazenamento disponível.
*   O Azure oferece uma variedade de tamanhos otimizados para diferentes cargas de trabalho (uso geral, otimizadas para computação/memória, GPU, alto desempenho).
*   O tamanho da VM afeta diretamente o custo.
*   É possível alterar o tamanho de uma VM em execução, desde que o novo tamanho esteja disponível no cluster de hardware atual.

### 5. Discos da VM
*   Toda VM Azure tem pelo menos dois discos: um disco do sistema operacional (OS disk) e um disco temporário.
*   **OS Disk**: Armazena o sistema operacional (geralmente 127GiB).
*   **Temporary Disk**: Fornece armazenamento temporário para aplicações e processos; **não armazene dados importantes aqui**, pois o conteúdo pode ser perdido.
*   **Data Disks (Opcional)**: Usados para armazenar dados de aplicações ou outros dados persistentes. São registrados como unidades SCSI e podem ser criptografados com Azure Disk Encryption.
*   Todos os discos são armazenados como **VHDs (Virtual Hard Disks)**.
*   **Managed Disks** (discos gerenciados) são o método **recomendado**. Eles abstraem as contas de armazenamento subjacentes, cuidando do provisionamento e gerenciamento de discos.
*   Tipos incluem Ultra SSD, Premium SSD, Standard SSD e Standard HDD.
*   Managed disks são automaticamente criptografados em repouso.

### 6. Sistema Operacional (Imagem)
*   O Azure oferece diversas imagens de SO, incluindo Windows e várias distribuições Linux (ex: UbuntuLTS).
*   A escolha do SO pode influenciar o preço de computação por hora.
*   Você pode usar imagens do **Azure Marketplace** (incluem SO e ferramentas de software populares) ou criar suas **próprias imagens personalizadas**.

## Métodos de Criação e Implantação de VMs

VMs podem ser criadas usando diversas ferramentas no Azure:

*   **Azure Portal**: Interface gráfica baseada na web, ideal para começar e realizar verificações visuais.
*   **Azure PowerShell**: Módulo que permite gerenciar recursos Azure via linha de comando.
*   **Azure CLI (Command-Line Interface)**: Ferramenta de linha de comando multiplataforma para gerenciar recursos Azure.
*   **Azure SDK**: Para desenvolvimento programático.
*   **REST API**: Para interações programáticas.
*   **Azure Resource Manager (ARM) Templates**: Arquivos JSON para implantação declarativa de um ou mais recursos, ideal para automação e consistência.
*   **Azure QuickStart Templates**: Modelos pré-configurados disponíveis no GitHub para implantação rápida.

## Conexão com Máquinas Virtuais

A conectividade segura às VMs é essencial.

*   **RDP (Remote Desktop Protocol)**: Para VMs Windows.
*   **SSH (Secure Shell)**: Para VMs Linux. Pode usar autenticação baseada em senha (menos segura) ou **SSH Key Pair** (recomendado, mais seguro).
*   **Azure Bastion**: Um serviço PaaS totalmente gerenciado que fornece conectividade RDP/SSH segura e contínua para VMs **diretamente no portal do Azure via SSL**, sem exigir IP público nas VMs. Isso protege as VMs da exposição direta das portas RDP/SSH à internet.


## Azure Availability Sets: Garantindo a Alta Disponibilidade

**Availability Sets** são um recurso crucial para construir soluções de nuvem confiáveis e garantir a **alta disponibilidade** de suas Máquinas Virtuais (VMs). Eles ajudam a proteger suas aplicações contra falhas de hardware ou eventos de manutenção planejada no datacenter do Azure.

## Conceito e Benefícios

Um Availability Set é um agrupamento lógico de VMs que informa ao Azure como seu aplicativo é construído para fornecer **redundância e disponibilidade**.

*   **Proteção contra Pontos Únicos de Falha**: Garante que um grupo de VMs relacionadas seja implantado de forma que um único ponto de falha (ex: falha de hardware, manutenção) não afete todas as VMs do seu aplicativo.
*   **Distribuição em Domínios de Falha (Fault Domains)**: O Azure distribui as VMs em diferentes racks de servidores (hardware físico, energia, rede) para isolamento de falhas de hardware. Se um rack falhar, as outras VMs do Availability Set continuarão funcionando.
*   **Distribuição em Domínios de Atualização (Update Domains)**: O Azure distribui as VMs em diferentes grupos de servidores que podem ser atualizados ou reiniciados sequencialmente durante a manutenção planejada. Isso garante que nem todas as VMs sejam offline ao mesmo tempo durante uma atualização do sistema operacional host.
*   **SLA (Service Level Agreement)**: A implantação de duas ou mais instâncias de VMs em um Availability Set proporciona um SLA de **99,95%** de disponibilidade para as VMs.
*   **Custos**: Não há custo adicional para o Availability Set em si; você paga apenas pelas instâncias de VM que criar.

## Como Implementar

Você pode criar Availability Sets e configurar VMs dentro deles usando o Azure Portal, modelos ARM, scripts ou ferramentas de API.

**Dicas de Implementação:**

*   **Configure múltiplas VMs** (duas ou mais) dentro de um Availability Set para redundância.
*   **Configure cada camada de aplicativo** (ex: web, aplicativo, banco de dados) em Availability Sets **separados**.
*   Combine um **Azure Load Balancer** com Availability Sets para distribuir o tráfego de entrada entre as instâncias da VM, garantindo alta disponibilidade e desempenho de rede.
*   Use **Managed Disks** com as VMs em um Availability Set.

## Availability Zones vs. Availability Sets

*   É importante distinguir entre Availability Sets e **Availability Zones**.
*   **Availability Zones** são locais físicos exclusivos dentro de uma região do Azure, cada um com energia, resfriamento e rede independentes.
*   Uma Availability Zone é uma combinação de um domínio de falha e um domínio de atualização.
*   Elas protegem aplicativos e dados contra falhas de datacenter.
*   A implantação em Availability Zones pode proporcionar um SLA de **99,99%** para suas VMs.

Para uma continuidade de negócios abrangente, a Microsoft recomenda construir a arquitetura do aplicativo usando uma **combinação de Availability Zones com pares de regiões do Azure**.

## Azure Virtual Machine Scale Sets (VMSS): Escalabilidade e Automação

**Virtual Machine Scale Sets (VMSS)** são um recurso do Azure que permite implantar e gerenciar um conjunto de **Máquinas Virtuais idênticas** e com balanceamento de carga. Eles são ideais para a execução de várias instâncias de sua aplicação, garantindo que se uma instância tiver um problema, os clientes ainda possam acessar sua aplicação através de outras instâncias com interrupção mínima.

## Conceito e Benefícios

VM Scale Sets são projetados para cenários de alta disponibilidade e escalabilidade, especialmente quando a demanda por sua aplicação varia.

*   **Implantação de VMs Idênticas**: Permitem implantar um conjunto de VMs com as mesmas configurações.
*   **Escalabilidade Automática (Autoscaling)**: A capacidade de ajustar automaticamente o número de instâncias de VM em resposta à demanda. Você pode definir regras baseadas em métricas de desempenho (ex: uso de CPU) para adicionar ou remover VMs, ou em agendamento. Isso ajuda a otimizar custos (removendo recursos ociosos) e a garantir o desempenho sob picos de carga.
*   **Gerenciamento Simplificado**: Facilita o gerenciamento de centenas de VMs sem tarefas adicionais de configuração ou gerenciamento de rede.
*   **Integração com Load Balancers**: Suportam o uso do Azure Load Balancer para distribuição de tráfego de camada 4 e Azure Application Gateway para distribuição de tráfego de camada 7.
*   **Pre-provisionamento de VMs não é necessário**: As VMs são adicionadas conforme a demanda aumenta e removidas quando a demanda diminui.

## Modos de Orquestração

Ao criar um VM Scale Set, você pode escolher como ele gerencia as máquinas virtuais:

*   **Modo de Orquestração Flexível**: Permite criar e adicionar manualmente uma VM de qualquer configuração ao conjunto de dimensionamento.
*   **Modo de Orquestração Uniforme**: Você define um modelo de máquina virtual, e o Azure gera instâncias idênticas com base nesse modelo.

## Diferenças com Availability Sets

Embora ambos os serviços visem a alta disponibilidade, suas principais diferenças são:

*   **Availability Sets** focam na distribuição de VMs em domínios de falha e atualização para proteger contra falhas de hardware e manutenção, ideal para um número fixo de VMs que precisam de alta disponibilidade.
*   **VM Scale Sets** focam na escalabilidade horizontal e automação, permitindo que o número de VMs seja ajustado dinamicamente para lidar com a carga. Eles são mais adequados para cargas de trabalho que exigem dimensionamento frequente.

**Dica**: Para VMs que são parte de um VM Scale Set, os instantâneos incrementais são armazenados como *page blobs*.

## Azure Resource Manager (ARM) Templates: Automação e Implantação Declarativa

**Azure Resource Manager (ARM) Templates** são uma ferramenta fundamental para a **automação, implantação e configuração** de recursos no Azure. Eles permitem que você defina a infraestrutura e a configuração do seu projeto em um arquivo JSON, seguindo uma sintaxe declarativa.

## O Papel do Azure Resource Manager (ARM)

O Azure Resource Manager (ARM) é o serviço de implantação e gerenciamento do Azure. Ele fornece uma camada de gerenciamento consistente que permite **criar, atualizar e excluir recursos** na sua conta Azure. Todas as interações com os recursos do Azure (seja via portal, PowerShell, CLI ou SDKs) passam por essa camada.

## O que são ARM Templates?

Um ARM Template é um arquivo **JSON** que descreve os recursos que você deseja implantar em um grupo de recursos.

### Sintaxe Declarativa

A principal característica dos ARM Templates é a **sintaxe declarativa**. Isso significa que você apenas **declara qual é o estado desejado** dos seus recursos, e o Azure Resource Manager se encarrega de implantá-los. Você não precisa especificar a sequência de comandos de programação.

### Benefícios dos ARM Templates

*   **Consistência**: Garante que as implantações sejam consistentes e repetíveis, evitando configurações manuais e propensas a erros.
*   **Automação**: Ideal para automatizar a implantação de infraestruturas complexas.
*   **Implantações Agrupadas**: Você pode implantar vários serviços e suas dependências em um único template como uma operação coesa.
*   **Reutilização**: Templates podem ser reutilizados em diferentes ambientes (desenvolvimento, teste, produção) ajustando parâmetros.
*   **Controle de Acesso**: Aplica controle de acesso via RBAC (Role-Based Access Control) nos recursos.

## Seções de um ARM Template

Um template ARM básico possui as seguintes seções mandatórias:

*   `$schema`: URL que aponta para o arquivo de esquema JSON que define a versão da linguagem do template.
*   `contentVersion`: Versão do template (geralmente "1.0.0.0").
*   `parameters`: Valores que podem ser fornecidos pelo usuário durante a implantação para personalizar o template (ex: nome da VM, localização).
*   `functions`: Funções definidas pelo usuário para reutilização lógica.
*   `variables`: Valores dinâmicos que podem ser usados ao longo do template para simplificar a criação.
*   `resources`: Define os recursos do Azure a serem implantados (ex: VMs, contas de armazenamento, redes virtuais). Esta é a seção central do template.
*   `outputs`: Retorna valores (ex: IP público de uma VM, endpoints de armazenamento) após a conclusão da implantação.

## Compondo e Exportando Templates

*   **Compondo**: Você pode escrever templates em qualquer editor de texto. No entanto, **Visual Studio Code** oferece IntelliSense e snippets de código que facilitam muito a criação. A extensão Azure Resource Manager para VS Code é muito útil.
*   **Exportando**: É possível exportar o template ARM de um recurso ou grupo de recursos já implantado no Azure via Portal, Azure PowerShell ou Azure CLI. Isso é útil para recuperação ou para entender a configuração de um recurso existente.
*   **Templates de Início Rápido (QuickStart Templates)**: A Microsoft oferece uma galeria de templates pré-construídos e contribuídos pela comunidade no GitHub (`https://azure.microsoft.com/en-in/resources/templates` ou `https://github.com/Azure/azure-quickstart-templates`). Estes templates são excelentes para aprender e iniciar rapidamente novas implantações.

## Validação e Gerenciamento

*   Antes da implantação, você pode **validar o template** usando a opção "what-if" no PowerShell ou o comando `az group deployment validate` no Azure CLI para verificar erros de sintaxe ou implantação.
*   Para armazenar templates de produção de forma privada, pode-se usar repositórios privados ou o Azure Storage.
