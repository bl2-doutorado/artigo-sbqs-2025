# Cloud-Native Applications Profile - Version 1.0.0

1. [Objective](#objective)
2. [Inspirations](#inspirations)
3. [Main elements](#main-elements)
   1. [Generic Node Types](#generic-node-types)
   2. [Kubernetes Specific Node Types](#kubernetes-specific-node-types)
4. [Cloud-Native Applications Profile definitions](#cloud-native-applications-profile-definitions)
   1. [Cloud-native application elements](#cloud-native-application-elements)
      1. [cloud_native.nodes.AbstractComponent](#cloud_nativenodesabstractcomponent)
         1. [Properties](#properties)
         2. [Attributes](#attributes)
         3. [Definition](#definition)
      2. [cloud_native.nodes.Service](#cloud_nativenodesservice)
         1. [Properties](#properties-1)
         2. [Attributes](#attributes-1)
         3. [Definition](#definition-1)
      3. [cloud_native.nodes.BackingService](#cloud_nativenodesbackingservice)
         1. [Properties](#properties-2)
         2. [Attributes](#attributes-2)
         3. [Definition](#definition-2)
      4. [cloud_native.nodes.StorageBackingService](#cloud_nativenodesstoragebackingservice)
         1. [Properties](#properties-3)
         2. [Attributes](#attributes-3)
         3. [Definition](#definition-3)
   2. [Compute infrastructure elements](#compute-infrastructure-elements)
      1. [cloud_native.nodes.AbstractInfrastructure](#cloud_nativenodesabstractinfrastructure)
         1. [Properties](#properties-4)
         2. [Attributes](#attributes-4)
         3. [Definition](#definition-4)
      2. [cloud_native.nodes.AbstractContainerOrchestratorCluster](#cloud_nativenodesabstractcontainerorchestratorcluster)
         1. [Properties](#properties-5)
         2. [Attributes](#attributes-5)
         3. [Definition](#definition-5)
   3. [Storage elements](#storage-elements)

## Objective

This profile empowers users to model cloud-native applications with TOSCA, tailoring solutions for various contexts, including clusterized applications. Because of TOSCA's extensibility, users can adapt its use to different cloud container orchestrators, such as Kubernetes, Nomad, or any other of their choice.

## Inspirations

We designed the profile following the ideas presented in the paper ["An Evaluation of Modeling Options for Cloud-native Application Architectures to Enable Quality Investigations"](https://ieeexplore.ieee.org/document/10061800/), which allows for evaluating software architectures of cloud-native applications according to quality aspects.
However, we did not strictly follow the TOSCA extension proposed in that paper since we could use the TOSCA Simple Profile better. We also needed to extend that work, adapting it to the clusterized applications universe and focusing on cloud architecture.

The paper mentioned earlier presents an interesting cloud-native architecture quality model formulated based on the [Quamoco Quality meta-model](https://ieeexplore.ieee.org/document/6227106/). The authors propose entities for modeling cloud-native applications.

## Main elements

This section summarizes the node types defined in this template and their functions. The [following subsection](#generic-node-types) defines generic node types, i.e., technology-independent ones. The following section presents Kubernetes-specific node types and shows templates' extensibility concerning cloud container orchestrators.

### Generic Node Types

The following table presents only generic node types (independent of technology):

| Type                              | Name                                         | Description                                                                             | Relationship                                        |
| --------------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------------- | --------------------------------------------------- |
| Cloud-native application elements | Abstract Component                           | An abstract element of the cloud-native application.                                    | Part of the application                             |
|                                   | Service                                      | Implements a business feature.                                                          | Derived from AbstractComponent                      |
|                                   | Backing Service                              | Implements common functionalities used by the services.                                 | Derived from AbstractComponent                      |
|                                   | Data Backing Service Storage                 | Data from other services.                                                               | Derived from AbstractComponent                      |
|                                   | Generic Component                            | Represents a component that can not be mapped to any other service kind                 | Derived from AbstractComponent                      |
| Compute infrastructure elements   | Abstract Compute Infrastructure              | Infrastructure used to execute the application and its dependencies (backing services). | Part of the application infrastructure              |
|                                   | Abstract Container Orchestrator Cluster      | A cluster managed by a Container Orchestrator like Kubernetes or Nomad.                 | Derived from Abstract Compute Infrastructure        |
|                                   | Abstract Container Orchestrator Cluster Node | A cluster node managed by a Container Orchestrator like Kubernetes or Nomad.            | Can join an Abstract Container Orchestrator Cluster |
| Storage elements                  | Abstract Storage                             | Stores data used by the native-cloud application elements.                              | Part of the storage                                 |
|                                   | Abstract Container Orchestrator Volume       | Implements the concept of a volume defined on the cluster.                              | Derived from Abstract Storage                       |

In the following Figure, we can see all these types and how they relate. In this class diagram, classes represent node types, inheritances
represent derivations of the node type, and associations represent relationship between node types (requirements from the source node type fulfilled by capabilities of the target node type).

![Node types and their relationships on the proposed template to model cloud-native applications.](figures/cloud-template-generic-node-types.png)

### Kubernetes Specific Node Types

| Type                              | Name                          | Description                                                                                                     | Relationship                                        |
| --------------------------------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| Cloud-native application elements | Kubernetes Application        | A Kubernetes deployable application that implements business features.                                          | Derived from Service                                |
|                                   | Kubernetes Dependency         | A Kubernetes deployable application that implements common functionalities used by the Kubernetes Applications. | Derived from Backing Service                        |
|                                   | Kubernetes Storage Dependency | A Kubernetes deployable application that stores data from other components.                                     | Derived from Storage Backing Service                |
|                                   | Kubernetes POD                | A Kubernetes POD.                                                                                               |                                                     |
| Compute infrastructure elements   | Kubernetes Cluster            | Kubernetes cluster managed by an specific Kubernete distribuition like K0s, K3s, RKE2, MicroK8s, among other.   | Derived from AbstractContainerOrchestratorCluster   |
|                                   | Kubernetes Node               | Physical or virtual machines that joined a Kubernetes cluster.                                                  | Joined on AbstractContainerOrchestratorCluster      |
| Storage elements                  | Abstract Kubernetes Volume    | Any kind of Kubernetes defined volume.                                                                          | Derived from Abstract Container Orchestrator Volume |

In the following Figure, for the sake of simplicity, we focus on Kubernetes-specific application element and how they relate. It is important to observe that attributes represent TOSCA properties, and attribute classes represent TOSCA Data Types, and according to TOSCA, all relationships of the parent node types are still valid to derived node types, and those relationships can be refined.

![Kubernetes-specific napplication ode types and their relationships on the proposed template to model cloud-native applications.](figures/cloud-template-kubernetes-specific-application-node-types.png)

In the next Figure, we focus on Kubernetes-specific compute infrastructure and storage elements.

![Kubernetes-specific compute infrastructure and storage node types and their relationships on the proposed template to model cloud-native applications.](figures/cloud-template-kubernetes-specific-infrastructure-and-data-node-types.png)

## Cloud-Native Applications Profile definitions

This section is normative and describes the properties, atributes, and definitions for all TOSCA node types defined for the Cloud-Native Applications Profile Version 1.0.0 specification, which are needed to describe a TOSCA Service template (in YAML).

### Cloud-native application elements

#### cloud_native.nodes.AbstractComponent

The Cloud-native Abstract Component Node Type is the default type that all other Cloud Native Application Node Types derive from. This node sets requirements, capabitilities and properties that derived Node Types must provide, in a consistent way.

##### Properties

| Name              | Required | Type                                                   | Constraints | Description                                                                                                        |
| ----------------- | -------- | ------------------------------------------------------ | ----------- | ------------------------------------------------------------------------------------------------------------------ |
| component_version | no       | tosca:version                                          | None        | The optional software component’s version. _Inherited from parent types._                                          |
| admin_credential  | no       | tosca.datatypes.Credential                             | None        | The optional credential that can be used to authenticate to the software component. _Inherited from parent types._ |
| metadata          | yes      | cloud_native.datatypes.AbstractComponentMetadata       | None        | Metadata of the application, for instance name, development team, version, description.                            |
| scalability       | yes      | tosca.capabilities.Scalable                            | None        |                                                                                                                    |
| updateStrategy    | yes      | cloud_native.datatypes.AbstractComponentUpdateStrategy | None        | Strategy used to update the application.                                                                           |

##### Attributes

| Name       | Required | Type   | Constraints      | Description                                                                                                                                                                                                                                                                                                                                               |
| ---------- | -------- | ------ | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tosca_id   | yes      | string | None             | A unique identifier of the realized instance of a Node Template that derives from any TOSCA normative type. \_Inherited from parent types.                                                                                                                                                                                                                |
| tosca_name | yes      | string | None             | This attribute reflects the name of the Node Template as defined in the TOSCA service template. This name is not unique to the realized instance model of corresponding deployed application as each template in the model can result in one or more instances (e.g., scaled) when orchestrated to a provider environment. _Inherited from parent types._ |
| state      | yes      | string | default: initial | The state of the node instance. Allowed values: initial, creating, created, configuring, configured, starting, started, stopping, deleting, error. _Inherited from parent types._                                                                                                                                                                         |

##### Definition

```yaml
cloud_native.nodes.AbstractComponent:
  description: An abstract element of the cloud-native application.
  derived_from: tosca.nodes.SoftwareComponent
  requirements:
    - host:
        description: Infrastructure needed to deploy the component
        count_range: [1, 1]
        node: cloud_native.nodes.AbstractInfrastructure
    - endpoint_link:
        description: Allows the definition of links between Components
        capability: tosca.capabilities.Endpoint
        relationship: tosca.relationships.ConnectsTo
        node: cloud_native.nodes.AbstractComponent
        count_range: [0, UNBOUNDED]
    - storage:
        description: Storage used by the component to persist data
        capability: tosca.capabilities.Storage
        relationship: gfinops.relationships.PersistsOn #tosca.relationships.AttachesTo
        count_range: [0, UNBOUNDED]
  capabilities:
    service_endpoint:
      description: A layer 4 endpoint provided by the component.
      type: tosca.capabilities.Endpoint
```

[TOSCA YAML](nodes/abstract_component.yaml)

#### cloud_native.nodes.Service

The Cloud-native Abstract Component Node Type is the default type that all other Cloud Native Application Node Types derive from. This node sets requirements, capabitilities and properties that derived Node Types must provide, in a consistent way.

##### Properties

Same from [cloud_native.nodes.AbstractComponent](#cloud_nativenodesabstractcomponent).

##### Attributes

Same from [cloud_native.nodes.AbstractComponent](#cloud_nativenodesabstractcomponent).

##### Definition

```yaml
cloud_native.nodes.Service:
  description: A cloud-native service.
  derived_from: cloud_native.nodes.AbstractComponent
```

[TOSCA YAML](nodes/service.yaml)

#### cloud_native.nodes.BackingService

Services that implement common functionalities used by the services like a log aggregator or a business process engine.

##### Properties

Same from [cloud_native.nodes.AbstractComponent](#cloud_nativenodesabstractcomponent).

##### Attributes

Same from [cloud_native.nodes.AbstractComponent](#cloud_nativenodesabstractcomponent).

##### Definition

```yaml
cloud_native.nodes.BackingService:
  description: A cloud-native backing service like a log aggregator or a business process engine.
  derived_from: cloud_native.nodes.AbstractComponent
```

[TOSCA YAML](nodes/backing_service.yaml)

#### cloud_native.nodes.StorageBackingService

Services that storage data like a SQL database, eg. PostgresSQL, a NoSQL database, eg. Cassandra, or any other type of storage backing service.

##### Properties

Same from [cloud_native.nodes.AbstractComponent](#cloud_nativenodesabstractcomponent).

##### Attributes

Same from [cloud_native.nodes.AbstractComponent](#cloud_nativenodesabstractcomponent).

##### Definition

```yaml
cloud_native.nodes.StorageBackingService:
  description: A cloud-native storage backing service like a SQL database, eg. PostgresSQL, a NoSQL database, eg. Cassandra, or any other type of storage backing service.
  derived_from: cloud_native.nodes.AbstractComponent
```

[TOSCA YAML](nodes/storage_backing_service.yaml)

### Compute infrastructure elements

#### cloud_native.nodes.AbstractInfrastructure

The Cloud-native Abstract Infrastructure Node Type is the default type that all other Cloud Native Infrastructure Node Types derive from. This node sets requirements, capabitilities and properties that derived Node Types must provide, in a consistent way.

##### Properties

| Name | Required | Type | Constraints | Description |
| ---- | -------- | ---- | ----------- | ----------- |
| N/A  | N/A      | N/A  | N/A         | N/A         |

##### Attributes

| Name            | Required | Type               | Constraints | Description                                                                                                                                        |
| --------------- | -------- | ------------------ | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| private_address | no       | string             | None        | The primary private IP address assigned by the cloud provider that applications may use to access the Compute node. \_Inherited from parent types. |
| public_address  | no       | string             | None        | The primary public IP address assigned by the cloud provider that applications may use to access the Compute node. \_Inherited from parent types.  |
| networks        | no       | map of NetworkInfo | None        | The map of logical networks assigned to the compute host instance and information about them. \_Inherited from parent types.                       |
| ports           | no       | map of PortInfo    | None        | The map of logical ports assigned to the compute host instance and information about them. \_Inherited from parent types.                          |

##### Definition

```yaml
cloud_native.nodes.AbstractInfrastructure:
  description: An abstract element of the cloud-native application.
  derived_from: tosca.nodes.Compute
```

[TOSCA YAML](nodes/abstract_infrastructure.yaml)

#### cloud_native.nodes.AbstractContainerOrchestratorCluster

The Cloud-native Abstract Infrastructure Node Type is the default type that all other Cloud Native Infrastructure Node Types derive from. This node sets requirements, capabitilities and properties that derived Node Types must provide, in a consistent way.

##### Properties

| Name | Required | Type | Constraints | Description |
| ---- | -------- | ---- | ----------- | ----------- |
| N/A  | N/A      | N/A  | N/A         | N/A         |

##### Attributes

| Name            | Required | Type               | Constraints | Description                                                                                                                                        |
| --------------- | -------- | ------------------ | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| private_address | no       | string             | None        | The primary private IP address assigned by the cloud provider that applications may use to access the Compute node. \_Inherited from parent types. |
| public_address  | no       | string             | None        | The primary public IP address assigned by the cloud provider that applications may use to access the Compute node. \_Inherited from parent types.  |
| networks        | no       | map of NetworkInfo | None        | The map of logical networks assigned to the compute host instance and information about them. \_Inherited from parent types.                       |
| ports           | no       | map of PortInfo    | None        | The map of logical ports assigned to the compute host instance and information about them. \_Inherited from parent types.                          |

##### Definition

```yaml
cloud_native.nodes.AbstractContainerOrchestratorCluster:
  description: A cluster managed by a Container Orchestrator like Kubernetes or Nomad.
  derived_from: cloud_native.nodes.AbstractInfrastructure
  properties:
    distribution:
      type: string
      required: true
    distribution_version:
      type: version
      required: true
```

[TOSCA YAML](nodes/abstract_container_orchestrator_cluster.yaml)

### Storage elements
