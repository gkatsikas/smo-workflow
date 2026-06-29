```plantuml

!theme mono

hide footbox
!pragma teoz true

skinparam BackgroundColor white
skinparam DefaultFontName Calibri
skinparam DefaultFontSize 20
skinparam SequenceMessageFontSize 14
skinparam SequenceMessageAlign center
skinparam MaxMessageSize 380

skinparam sequence {
    ArrowColor black
    LifeLineBorderColor #777777
    ParticipantBorderColor black
    ParticipantFontStyle bold
    ParticipantFontColor #000000
    SequenceMessageFontName Calibri

    GroupBorderColor #C55A11
    GroupBackgroundColor #d89b73
    GroupFontSize 30
    GroupFontStyle bold
}

!define SMO_BOX_COLOR LightGrey
!define SMO_BLOCK_COLOR FFFFFF

!define DATA_PLANE_BOX_COLOR Wheat
!define DATA_PLANE_BLOCK_COLOR F0F0F0

!define SDN_BLOCK_COLOR 8BC3EB

!define OTHER_COLOR D6DCE5

actor Operator

box "<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/oran-logo.png{scale=0.05}>  <size:28>SMO</size>" #SMO_BOX_COLOR
    participant "UI\n<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/dashboard-logo.jpg{scale=0.1}>" as UI #SMO_BLOCK_COLOR
    participant "NBI\n<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/api-logo.png{scale=0.065}>" as NBI #SMO_BLOCK_COLOR
    participant "Orchestrator\n<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/etsi-hypo-logo-black-cropped.png{scale=0.25}>" as HypO #SMO_BLOCK_COLOR
    participant "Telemetry\n<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/prometheus-logo.png{scale=0.0287}>" as MON #SMO_BLOCK_COLOR

    participant "rApp\n<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/rapp-logo.png{scale=0.082}>" as RAPP #SMO_BLOCK_COLOR
end box

participant "SDN Controller\n<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/etsi-tfs-logo.png{scale=0.135}>" as TFS #SDN_BLOCK_COLOR

box "<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/oran-logo.png{scale=0.05}> <size:28>Data Plane</size>" #DATA_PLANE_BOX_COLOR
    participant "Infrastructure\n<img:https://raw.githubusercontent.com/gkatsikas/smo-workflow/refs/heads/master/images/infra-logo.jpg{scale=0.0295}>" as RAN #DATA_PLANE_BLOCK_COLOR
end box

group <size:20> Functional Split Deployment
    Operator --> UI: Request RAN Functional Split (FS) service and Transport Network (TN) configuration
    UI -> NBI:
    NBI -> HypO: TMF-based request\nfor FS deployment
    HypO -> RAN: Instantiate software RAN\ncomponents of desired FS
    NBI -> TFS: IETF-based TN slice request to support the deployed FS
    TFS -> RAN: Configure TN
    RAN -> MON: Publish periodic FS telemetry data
    UI -> MON: Pull metrics into SMO dashboard
    UI --> Operator: Visualize active FS and TN status
end

group <size:20> rApp Deployment and Metrics' Exposure
    Operator --> UI: Request policy-driven\nrApp deployment
    UI -> NBI:
    NBI -> HypO: Onboard and deploy rApp through standards-based NBIs
    HypO -> RAPP: Instantiate rApp on a control\nplane cluster node
    RAPP -> MON: Subscribe to relevant\nmetrics based on policy
end

group <size:20> Closed-loop Adaptation
    RAPP -> RAPP: Evaluate metrics against policy
    RAPP -> NBI: Trigger adaptation request
    NBI -> HypO: Deploy new FS service
    HypO -> RAN: Instantiate software RAN\ncomponents of new FS
    HypO -> RAN: De-activate new FS service
    NBI -> HypO: Tear old FS service down
    HypO -> RAN: Deprovision software RAN\ncomponents of old FS
    NBI -> TFS: Update TN slice to accommodate the new FS
    HypO -> RAN: Activate new FS service
    NBI -> UI: Update FS service and TN slice
    MON -> UI: Update dashboard
    UI --> Operator: Visualize new FS and TN status
end

```
