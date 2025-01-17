---

copyright:
  years: 2017, 2018, 2019

lastupdated: "2019-06-04"

keywords: resource, policies, authorization, resource type, resource groups, roles, load balancer, VPN, operator, editor, viewer, admin

subcollection: vpc-on-classic

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:DomainName: data-hd-keyref="DomainName"}

# 关于 VPC 基础架构资源
{: #about-vpc-infrastructure-resources}

术语_资源_是指 {{site.data.keyword.cloud}} Virtual Private Cloud 部署中的组件部分。每个 VPC 可能包含以下类型的资源，这些资源可以根据需要分组在一起：

* **VPC**
* **实例**
* **密钥**
* **映像**
* **子网**
* **卷**
* **网络访问控制表 (ACL)**
* **安全组**
* **浮动 IP**
* **VNIC**
* **网关**
* **负载均衡器**
* **VPN**

一些 VPC 基础架构资源会从父 VPC 继承其授权策略，用于管理自身的使用情况，但另一些资源是单独管理的。

目前，`实例`、`密钥`、`安全组`、`卷`、`负载均衡器`和 `VPN` 资源类型会维护其自己的授权策略。本文档后面部分中提供了有关这些资源的资源授权的更多详细信息。
{: note}

## 资源策略
{: #resource-policies}

通常，对 VPC 中资源的访问权通过_策略_进行控制。如果要定制对 VPC 资源的访问权，可以创建定制策略。资源策略可以将以下资源设定为目标：

* 所有资源
* 某种类型的所有资源
* 某个组中的所有资源
* 单个资源

## 资源和资源组
{: #resources-and-resource-groups}

_资源组_是资源的集合，如整个 VPC 或单个子网及其 ACL，这些资源关联在一起，用于确定授权和用途。您可以将资源组视为可由项目、部门或团队使用的基础架构集合。

可以使用搜索 CLI 来显示附加到资源的标记。通过相应的过滤器，可以仅显示您关注的实例，例如 `ibmcloud resource search name:` 或 `ibmcloud resource search 'service_name:is AND type:vpc'`。
{: tip}

大型企业可能希望将 VPC 划分成资源组。但规模较小的公司则可能不需要将其 VPC 划分成资源组，因为其整个团队都将使用整个 VPC。如果您熟悉 _OpenStack_，那么资源组在概念上类似于 _OpenStack Keystone_ 中的_项目_。

仅当创建 VPC 时，才能将资源分配给资源组。创建资源后，即不能更改资源所属的资源组。
{: .note}

资源组主要设计用于大型组织。对于大多数公司和团队而言，资源细分是在 VPC 边界执行的。然而，当可以将单个 VSI（实例）分配给资源组并将单个用户分配给 VSI（实例）资源时，此一般经验法则可能会更改。

设置 IBM Cloud VPC 时，如果要使用资源组，最好针对希望如何将组织中的资源和用户分配给每个资源组，事先制定相应计划。
{: tip}

## VPC 的特性
{: #specifics-for-vpc}

一些 VPC 基础架构资源会从帐户或父 VPC 继承其授权策略，用于管理自身的使用情况，而另一些资源具有单独的策略。

### VPC 资源授权（按资源类型）
{: #vpc-resource-authorization-by-resource-type}

下表概述了缺省情况下 VPC 资源的授权方式。

|资源类型|在何处获取其缺省授权|
|-----------|------------|
|VPC|创建时的授权检查|
|负载均衡器|创建时的授权检查|
|VPN|创建时的授权检查|
|子网|父 VPC|
|公共网关|父 VPC|
|实例|创建时的授权检查|
|安全组|创建时的授权检查|
|密钥|创建时的授权检查|
|映像|帐户|
|浮动 IP|帐户|
|网络 ACL|帐户|
|卷|创建时的授权检查|

创建浮动 IP 和网络 ACL 时可以在帐户级别限定作用域（如果未分配浮动 IP 和网络 ACL）。但是，在将浮动 IP 分配给实例或将 ACL 分配给子网后，这些资源即遵从 VPC 的授权作用域。
{: note}

### VPC 对资源的角色和授权操作的覆盖范围
{: #vpc-coverage-of-roles-and-authorized-actions-on-resources}

下面的一些示例说明了在有 2 个 VPC 的方案中，具有特定角色的任何用户尝试执行特定操作时会发生的情况：

* _管理员_用户，具有对所有资源的**管理员**许可权
  * 创建 `vpc1`：可以

* _编辑者_用户，具有对所有资源的**编辑者**许可权
  * 创建 `vpc2`：可以
  * 更新 `vpc2`：可以
  * 删除 `vpc2`：可以

* _操作员_用户，具有对所有资源的**操作员**许可权
  * 创建 `vpc`：被拒绝
  * 更新 `vpc1`：被拒绝
  * 删除 `vpc1`：被拒绝

* _查看者_用户，具有对所有资源的**查看者**许可权
  * 列出 VPC：显示 [vpc1]
  * 读取 `vpc1`：可以

* _无角色_用户，不具有任何策略
  * 列出 VPC：显示 []
  * 读取 `vpc1`：被拒绝

### VPC 覆盖范围摘要表
{: #vpc-coverage-summary-table}

对于通过角色（管理员、编辑者、操作员和查看者）授予访问权的任何策略，会提供以下操作（X=被拒绝，o=可以执行）

角色：|无|查看者|操作员|编辑者|管理员
-----------:|------|--------|-------|--------|-------
创建|X|X|X|o|o
列出|X|o|X|o|o
读取|X|o|X|o|o
更新|X|X|X|o|o
删除|X|X|X|o|o


## VPN for VPC 的资源授权
{: #resource-authorizations-of-vpn-for-vpc}

**VPN for VPC** 的资源授权与其他类型的 VPC 资源授权分开设置，但设置方式类似。

VPN for VPC 中的策略控制对 VPN 资源（特别是 VPN 网关）的访问权。要访问 VPN 网关的子资源（例如，VPN 连接以及本地或同级 CIDR），您必须对父 VPN 网关具有**读**访问权（或更多访问权）。资源策略只能用于对父 VPN 网关具有**读**访问权或更高访问权的 VPN 连接。VPN 中的资源策略可以将以下资源设定为目标：

* 所有 VPN 资源（VPN 网关、VPN 连接、IKE 和 IPsec 策略）
* 资源组中的所有 VPN 资源
* 单个 VPN 网关

VPN 使用量也会单独计费。
{: note}

### VPN for VPC 对资源的角色和授权操作的覆盖范围
{: #vpn-for-vpc-coverage-of-roles-and-authorized-actions-on-resources}

支持的用户角色与在 VPC 资源授权中支持的相同，但针对每个角色启用的操作不同。

下面的一些示例说明了具有特定角色的用户尝试执行与 VPN 相关的特定操作时会发生的情况：

* _管理员_用户，具有对所有资源的**管理员**许可权
  * 创建、更新、删除、读取和列出 VPN 网关：可以
  * 创建、更新、删除、读取和列出 VPN 连接：可以
  * 创建、更新、删除、读取和列出 VPN 连接同级和本地 CIDR：可以
  * 创建、更新、删除、读取和列出 IKE 策略：可以
  * 创建、更新、删除、读取和列出 IPSec 策略：可以

* _编辑者_用户，具有对所有资源的**编辑者**许可权
  * 与管理员可执行的操作相同

* _操作员_用户，具有对所有资源的**操作员**许可权
  * 创建、更新和删除 VPN 网关：被拒绝
  * 创建、更新和删除 VPN 连接：被拒绝
  * 创建、更新和删除 VPN 连接同级和本地 CIDR：被拒绝
  * 创建、更新和删除 IKE 策略：被拒绝
  * 创建、更新和删除 IPSec 策略：被拒绝
  * 读取和列出 VPN 网关：可以 - 显示实际数据
  * 读取和列出 VPN 连接：可以 - 显示实际数据
  * 读取和列出 VPN 连接同级和本地 CIDR：可以 - 显示实际数据
  * 读取和列出 IKE 策略：可以 - 显示实际数据
  * 读取和列出 IPSec 策略：可以 - 显示实际数据

* _查看者_用户，具有对所有资源的**查看者**许可权
  * 与操作员可执行的操作相同

* _无角色_用户，不具有任何策略
  * 读取和列出 VPN 网关：被拒绝 - 列表显示空数据
  * 读取和列出 VPN 连接：被拒绝
  * 读取和列出 VPN 连接同级和本地 CIDR：被拒绝
  * 读取和列出 IKE 策略：被拒绝 - 列表显示空数据
  * 读取和列出 IPSec 策略：被拒绝 - 列表显示空数据

### VPN for VPC 覆盖范围摘要表
{: #vpn-for-vpc-coverage-summary-table}

VPN for VPC 中的操作角色映射可在下表中直观显示（X=被拒绝，o=可以执行）：

角色：|无|查看者|操作员|编辑者|管理员
-----------:|------|--------|-------|--------|-------
创建|X|X|X|o|o
列出|X|o|o|o|o
读取|X|o|o|o|o
更新|X|X|X|o|o
删除|X|X|X|o|o

## Load Balancer for VPC 的资源授权
{: #resource-authorization-for-load-balancer-for-vpc}

针对 **Load Balancer for VPC** 使用量的资源授权与 VPC 中的其他资源授权分开设置，但设置方式类似。

负载均衡器使用量也会单独计费。
{: note}

### 负载均衡器对资源的角色和授权操作的覆盖范围
{: #load-balancer-coverage-of-roles-and-authorized-actions-on-resources}

下面的一些示例说明了具有特定角色的用户尝试执行与 VPN 负载均衡器相关的特定操作时会发生的情况：

* _管理员_用户，具有对所有资源的**管理员**许可权
  * 创建、更新、删除、读取和列出负载均衡器：可以
  * 创建、更新、删除、读取和列出侦听器：可以
  * 创建、更新、删除、读取和列出池：可以
  * 创建、更新、删除、读取和列出成员：可以
  * 创建、更新、删除、读取和列出负载均衡器统计信息：可以

* _编辑者_用户，具有对所有资源的**编辑者**许可权
  * 与管理员可执行的操作相同

* _操作员_用户，具有对所有资源的**操作员**许可权
  * 创建、更新、删除、读取和列出负载均衡器：被拒绝
  * 创建、更新、删除、读取和列出侦听器：被拒绝
  * 创建、更新、删除、读取和列出池：被拒绝
  * 创建、更新、删除、读取和列出成员：被拒绝
  * 创建、更新、删除、读取和列出负载均衡器统计信息：被拒绝

* _查看者_用户，具有对所有资源的**查看者**许可权
  * 读取和列出负载均衡器：可以
  * 读取和列出侦听器：可以
  * 读取和列出池：可以
  * 读取和列出成员：可以
  * 读取负载均衡器统计信息：可以

* _无角色_用户，不具有任何策略
  * 读取和列出负载均衡器：被拒绝 - 列表显示空数据
  * 读取和列出侦听器：被拒绝
  * 读取和列出池：被拒绝
  * 读取和列出成员：被拒绝
  * 读取负载均衡器统计信息：被拒绝

### 负载均衡器覆盖范围摘要表
{: #load-balancer-coverage-summary-table}

Load Balancer for VPC 中的操作角色映射可在下表中直观显示（X=被拒绝，o=可以执行）：

角色：|无|查看者|操作员|编辑者|管理员
-----------:|------|--------|-------|--------|-------
创建|X|X|X|o|o
列出|X|o|X|o|o
读取|X|o|X|o|o
更新|X|X|X|o|o
删除|X|X|X|o|o


## Virtual Server for VPC 的资源授权
{: #planning-virtual-servers-for-vpc-permissions}

**Virtual Server for VPC** 的资源授权独立于 VPC 中的其他资源授权进行设置。 

Virtual Servers for VPC 使用量将单独计费。
{: note}

* 作为管理员，您可以定义角色以及对 {{site.data.keyword.vsi_is_short}} 执行任何可用的操作。
* 作为编辑者，您可以修改状态以及创建或删除子资源。
* 作为操作员，您可以执行不更改资源状态的操作。
* 作为查看者，您可以执行不更改资源状态的操作。

### 虚拟服务器覆盖范围摘要表
{: #vsi-coverage-summary-table}

Virtual Server for VPC 中的操作角色映射可在下表中直观显示（X=被拒绝，o=可以执行）：

角色：|无|查看者|操作员|编辑者|管理员
-----------:|------|--------|-------|--------|-------
创建|X|X|X|o|o
列出|X|o|o|o|o
读取|X|o|o|o|o
更新|X|X|X|o|o
删除|X|X|X|o|o

创建实例时，如果指定了 VPC 和安全组资源，那么您还必须具有对这些资源的操作员访问权。子网和浮动 IP 资源从关联的 VPC 继承许可权。  
{: tip} 
