---
layout: default
title: Workflow
nav_order: 4
has_children: false
---

# Workflow

- it is techinically a cronjob ( `WorkflowModel extends CronJobModel`) but not fully automated and required decisions on each action. `WorkflowTemplate` is the job definition ( `WorkflowTemplateModel extends JobModel` ).

## Key Concepts

- `Workflow Template` A template for a workflow.  It consists of workflow action templates and defines the basic sequence of a workflow in the form of options.
- `Workflow Action Template` - A template for a workflow action. Logically, a definition of a step in a workflow. It is linked to other workflow action templates via options.
- `Option` - Defines a possible result of a workflow action template, that is, is a template for a decision. It also defines which steps might follow after the current workflow action Template.
- `Workflow` - Represents an actual workflow. It is created by copying from a workflow template.
- `Workflow Action` - Represents an individual step within a workflow. It is copied from a workflow action template in the process of copying a workflow from a workflow template.
- `Decision` - The actual choice of the options selected. A decision stores the result of the action.
- `Comment` - A note left on a workflow action. For example, to leave a piece of information about how to tackle the upcoming workflow action, or to keep track of how somebody worked on the current workflow action.
- `VisibleForPrincipals` - An attribute of a workflow template, used for assigning users to a particular workflow template in order to enable users to see and create new workflows. If the user has no template assigned, then the workflows templates section is not shown at all.
- `WorkflowDecisionTemplate` - outbound links from an `Workflow Action Template` to another `Workflow Action Template`. it holds initial attributes that are passed to `Workflow Actions`
- `WorkflowItemAttachment` - holds a reference to an item required the current context of workflow

```
Workflow Template           ---->   Workflow
Workflow Action Template    ---->   Workflow Action
Options                     ---->   Decisions 
```

## User roles

- `Workflow Modeler/Workflow Designer` - Sets up and defines workflow templates.
- `Workflow Manager` - Creates actual workflows from workflow templates.
- `Workflow Assignees` - In-house employees who perform individual steps on a workflow.

## Status of Workflow Action

- `pending` - at least one of its preceding actions has not been completed
- `in progress` - this action is unlocked and can be worked on
- `completed` - action has been dealt-with
- `disabled` - depricated status
- `ended through end of workflow` - action that has marked as end has reached

## Type of Workflow Action

- `start` - Beginning points of a workflow.
- `normal` - Neither beginning points nor completion points of a workflow.
- `end` - Completion points of a workflow.

## Conditions on Options

- `OR-connected` - target workflow action template is activated if at least one of the incoming options is set to the respective value later on
- `AND-connected` - target workflow action template is activated only if all of the incoming options are set to the respective value later on

## Procedures

- `Workflow Managers` create a `Workflow` from the `Workflow Template`
- `Workflow Actions` that are marked as `start` are automatically set to `in progress` status, other actions are set to `pending` status
- Actions are assigned to `User` or `UserGroup`. They can view in the Backoffice Inbox or Product Cockpit. Email can also be configured. 
- They work on the action and select the potential decision in the options. This triggers the next action.
- `AutomatedWorkflowActionTemplate` - automated workflow actions can be configured through `AutomatedWorkflowTemplateJob` job definitions 
- comments and attachments are available to workflow actions
- if workflow reaches the workflow action marked as `end`, then all workflow actions in the Workflow are marked `ended through end of workflow` 

## Workflow Activation Script

- each `Workflow Template` has an individual activation script that returns a Boolean value
- it is evaluated when an item is created, saved and removed

 