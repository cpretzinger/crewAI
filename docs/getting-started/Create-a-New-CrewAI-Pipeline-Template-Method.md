# Creating a CrewAI Pipeline Project

Welcome to the comprehensive guide for creating a new CrewAI pipeline project. This document will walk you through the steps to create, customize, and run your CrewAI pipeline project, ensuring you have everything you need to get started.

To learn more about CrewAI pipelines, visit the [CrewAI documentation](https://docs.crewai.com/core-concepts/Pipeline/).

## Prerequisites

Before getting started with CrewAI pipelines, make sure that you have installed CrewAI via pip:

```shell
$ pip install crewai crewai-tools
```

The same prerequisites for virtual environments and Code IDEs apply as in regular CrewAI projects.

## Creating a New Pipeline Project

To create a new CrewAI pipeline project, you have two options:

1. For a basic pipeline template:

```shell
$ crewai create pipeline <project_name>
```

2. For a pipeline example that includes a router:

```shell
$ crewai create pipeline --router <project_name>
```

These commands will create a new project folder with the following structure:

```
<project_name>/
├── README.md
├── uv.lock
├── pyproject.toml
├── src/
│   └── <project_name>/
│       ├── __init__.py
│       ├── main.py
│       ├── crews/
│       │   ├── crew1/
│       │   │   ├── crew1.py
│       │   │   └── config/
│       │   │       ├── agents.yaml
│       │   │       └── tasks.yaml
│       │   ├── crew2/
│       │   │   ├── crew2.py
│       │   │   └── config/
│       │   │       ├── agents.yaml
│       │   │       └── tasks.yaml
│       ├── pipelines/
│       │   ├── __init__.py
│       │   ├── pipeline1.py
│       │   └── pipeline2.py
│       └── tools/
│           ├── __init__.py
│           └── custom_tool.py
└── tests/
```

## Customizing Your Pipeline Project

To customize your pipeline project, you can:

1. Modify the crew files in `src/<project_name>/crews/` to define your agents and tasks for each crew.
2. Modify the pipeline files in `src/<project_name>/pipelines/` to define your pipeline structure.
3. Modify `src/<project_name>/main.py` to set up and run your pipelines.
4. Add your environment variables into the `.env` file.

## Example 1: Defining a Two-Stage Sequential Pipeline

Here's an example of how to define a pipeline with sequential stages in `src/<project_name>/pipelines/pipeline.py`:

```python
from crewai import Pipeline
from crewai.project import PipelineBase
from ..crews.research_crew.research_crew import ResearchCrew
from ..crews.write_x_crew.write_x_crew import WriteXCrew

@PipelineBase
class SequentialPipeline:
    def __init__(self):
        # Initialize crews
        self.research_crew = ResearchCrew().crew()
        self.write_x_crew = WriteXCrew().crew()

    def create_pipeline(self):
        return Pipeline(
            stages=[
                self.research_crew,
                self.write_x_crew
            ]
        )

    async def kickoff(self, inputs):
        pipeline = self.create_pipeline()
        results = await pipeline.kickoff(inputs)
        return results
```

## Example 2: Defining a Two-Stage Pipeline with Parallel Execution

```python
from crewai import Pipeline
from crewai.project import PipelineBase
from ..crews.research_crew.research_crew import ResearchCrew
from ..crews.write_x_crew.write_x_crew import WriteXCrew
from ..crews.write_linkedin_crew.write_linkedin_crew import WriteLinkedInCrew

@PipelineBase
class ParallelExecutionPipeline:
    def __init__(self):
        # Initialize crews
        self.research_crew = ResearchCrew().crew()
        self.write_x_crew = WriteXCrew().crew()
        self.write_linkedin_crew = WriteLinkedInCrew().crew()

    def create_pipeline(self):
        return Pipeline(
            stages=[
                self.research_crew,
                [self.write_x_crew, self.write_linkedin_crew]  # Parallel execution
            ]
        )

    async def kickoff(self, inputs):
        pipeline = self.create_pipeline()
        results = await pipeline.kickoff(inputs)
        return results
```

### Annotations

The main annotation you'll use for pipelines is `@PipelineBase`. This annotation is used to decorate your pipeline classes, similar to how `@CrewBase` is used for crews.

## Installing Dependencies

To install the dependencies for your project, use `uv` the install command is optional because when running `crewai run`, it will automatically install the dependencies for you:

```shell
$ cd <project_name>
$ crewai install (optional)
```

## Running Your Pipeline Project

To run your pipeline project, use the following command:

```shell
$ crewai run
```

This will initialize your pipeline and begin task execution as defined in your `main.py` file.

## Deploying Your Pipeline Project

Pipelines can be deployed in the same way as regular CrewAI projects. The easiest way is through [CrewAI+](https://www.crewai.com/crewaiplus), where you can deploy your pipeline in a few clicks.

Remember, when working with pipelines, you're orchestrating multiple crews to work together in a sequence or parallel fashion. This allows for more complex workflows and information processing tasks.
