---
layout: post
title:  "Azure Pipelines | Variable conditions"
date:   2019-10-17 11:00:00
categories: azure pipeline devops
---
# >_

## Scenario

Let's imagine that, whatever reason you have, you have a build definition on **Azure Devops** that is running using **Azure Pipelines** through an **azure-pipelines.yml** file and should have conditional step execution based, uppon others, on a variable value that is settable on ***Pipeline Run*** time.

How do you should configure your pipeline?

## Setup

Let's start from the [empty template](https://github.com/microsoft/azure-pipelines-yaml/blob/master/templates/empty.yml) that Microsoft bring us from their azure-pipelines-yaml GitHub repository.

Just remove the unnecessary parts of the template... and let's start!

## Conditions

Regardless the [official documentation](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/conditions?view=azure-devops&tabs=yaml) is pretty complete, there are some concepts to have clear.

By default, any step, job or stage is executed after the previous one succeeded.

```yaml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- script: echo Hello, world, to everybody, without condition!
  displayName: 'Run a one-line script - World'

```

But, when you want to add some kind of logic to your build definition, and select when to execute a given step based on a value, you should add the `condition` property on your desired step.

Be carefull, as the file format is **yaml**, the leading spaces should be exactly two (2) in this case.

```yaml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- script: echo Hello, world, to everybody, without condition!
  displayName: 'Run a one-line script - World'

- script: echo Hello, failed!
  displayName: 'Run a one-line script - Failed'
  condition: failed() # Only runs when previous step fails, aka, probably never in this case.

```

## Variables

Any Azure Pipeline definition have, by default, a lot of [process variables](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch) that can be used on your pipeline execution steps only referencing variable name enclosed in `$()`.

When you want to create a custom variable, you have to setup it from the azure pipeline edition form. To add any new custom variable (as parameter) to your pipeline, just go to **Pipelines / <Your_Build_Pipeline_Name> / Edit > Variables**.

![Azure Pipelines 'new variable' form](/assets/azure-pipelines-variable-conditions-1.jpg)

> Tip: Please, don't ignore the naming info and suggestions provided on this form, as they are very important when you want to reference your variables from the *azure-pipeline.yml* file.

In example, let's declare `HelloDevs` variable. This variable will be `true` or `false`, and will be used on the pipeline to execute a new step that will "say" (write on the stdout with `echo` command) hello to all devs in the world.

```yaml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- script: echo Hello, world, to everybody, without condition!
  displayName: 'Run a one-line script - World'

- script: echo Hello, Devs!
  displayName: 'Run a one-line script - Devs'
  condition: and(succeeded(), eq(variables.HelloDevs, ${{ true }}))
```

Let's analyze it, using the [official documentation](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/expressions?view=azure-devops#functions):

* The `and()` method allow to evaluate all the parameters are `true`
* The `succeded()` method evaluates if the previous step has been completed successfully
* The `eq()` method evaluates that the first parameter value is equal to the second one
* The `${{ true }}` expression converts the `true` value into boolen, that allow the `eq()` method to compare any string received (`"true"`, or `"True"`, or `"TRUE"`...)

So, here the conditions is:

> **Ensure that the previous step is succeded and the `HelloDevs` variable is `true`**

To reference a variable, in this case `HelloDevs`, you have two options:

* Reference it through `variable['name']` or
* Reference it through `variable.name`

## Use it

Just run your pipeline, and edit the variable clicking on it, change the variable value and click **Run**.

![Azure Pipelines 'Run pipeline' form](/assets/azure-pipelines-variable-conditions-2.jpg)

And the result of this execution, with the `HelloDevs` variable set to `true`, will be something like:

![Azure Pipelines execution result](/assets/azure-pipelines-variable-conditions-3.jpg)
