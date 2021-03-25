:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. note::

   This tech note details some basic concepts about how users will interact with the Vera Rubin Observatory control system during commissioning and operations.


.. _Introduction:

Introduction
============

The construction, integration, commissioning and operation of observatories requires a significant amount of knowledge spanning many disciplines.
However, all operational aspects are performed using some piece of control software, whether it be embedded low-level servo control to enable precise positioning of the mount to a simple graphical display of the current weather conditions.
The observatory control system, which encompasses numerous levels of control, must span a large range of functionalities that are applicable to numerous users, all with varying experience levels and expectations regarding ease-of-use.
Therefore, from within this centralized stack of code, a high degree of flexibility must be maintained, while providing a user-friendly framework to all levels of users.
The top-level functionality specifications that drives the development of the observatory control system are:

  - Regular operations routines should be easily accessible;
  - Must include a highly customizable scripting capability must be available and able to run user-written code;
  - An interactive mode, equivalent to a user sandbox space, must be available to allow development of routines on the fly as well as debugging;
  - Capability to incorporate user-developed features;

In the standard operational model used to perform the survey, the observatory will be largely driven by the Scheduler, which will designate a visit script, which consists of a series of operations that are performed including slewing the telescope and taking an image.
The script will be execution will be automated while the user will be able to observe (or interrupt) the progress via a graphical interface.
It is expected that operators will be much more involved during the start-up and shutdown phases, as well as during afternoon calibrations.
In the majority of cases, the operations will be routine and are expected to be done from a high-level of the observatory control software, as is discussed further in following sections.

During integration, commissioning, and engineering time, there will be situations where users will want to execute non-standard, customized operations which require an increased level of flexibility and control.
As to be expected, the additional functionality required to perform such tasks requires more in-depth knowledge specific areas of the system and therefore require a knowledge base and toolset beyond what is typical of an observing specialist which performs nighttime operations.
This evolves further as one considers what tooling and knowledge is required for development of the system, however, all of the above users and usages require those interactive tools.

The breadth of the observatory control system allows users to perform both these routine and customized tasks via three main interactive components: the :ref:`LSST Observing Visualization Environment (LOVE)<LOVE>`, :ref:`ScriptQueue <Script-Queue>` and :ref:`Jupyter notebooks <Jupyter-notebooks>`.
This document begins with a high-level overview of the systems and explore how users can take advantage of their features to accomplish different types of tasks
References to more detailed documentation regarding their operation are linked throughout..

Then, to assist in defining the expectations and knowledge levels associated with the multiple complexities associated with all of the required tasks that must be performed by the OCS, we've identified three main actors and their expected roles and ability levels associated their interactions with the control system.
The identified actors are: 1) Observing Specialists, 2) Commissioning Scientists, 3) Software Developers.
In reality, we expect (and need) several people to have significant overlap between these roles.

The documentation associated with the example procedures expected to be performed by each of the actors are then linked to provide a demonstration of the current usability level associated with each task.

Under the standard operational procedures used to perform the survey, the observatory will be largely driven by the Scheduler, which will designate a visit script, which consists of a series of operations that are performed via a content controlled script. The script will be executed by the scriptQueue, and the user will be able to observe (or interrupt) the progress via the LOVE interface.
It is expected that operators will be much more involved during the start-up and shutdown phases, as well as during afternoon calibrations.
In the majority of cases, the operations will be routine and are expected to be done from a high-level of the observatory control software, as is discussed further in following sections.

Lastly, using concrete examples, we present examples of the types of interactions that these actors will perform with links to procedures on how to do them using the multiple interfaces.
These examples explore how users can take advantage of the features also include cases of how *not* to use an interface due to its limitations or inefficiencies in some areas.
Discussions regarding the ease-of-use and required additional features can then use these examples as a basis for the conversation.

.. _LOVE:

LOVE
====

Tiago insert (or link) content here.

Important to say that users will interact with basic observatory operations via LOVE, but what is actually happening on the back end is that these operations (e.g. offsets) are actually scripts that get loaded and performed.


.. _Script-Queue:

Script Queue
============

The `ScriptQueue`_ is the main hub for the execution of regular operations that require a minimum level of interaction.
As mentioned above in the `LOVE`_ section, although a user-interface to perform an operation, such as a telescope offset, will be way in which a user performs the offset, the actual action will be a :ref:`SAL Script <SAL_Scripts>` being loaded and executed by the `ScriptQueue`_.
The capabilities and functionalities executed by the scriptQueue will be very large.
For instance, when operators need to prepare the observatory for calibrations or for night operation, it will be possible to add a script to the ScriptQueue that will perform all the required tasks (e.g. position telescope and dome, perform required fine tuning in positioning, open mirror covers and so on).
Likewise, the ScriptQueue is the main tool observers will rely on to perform regular nighttime operations, e.g., track a target and obtain a set of standard observations.
As soon as one script finishes, the next one begins until no scripts remain to be run.
The Scheduler for the survey essentially adds scripts to the scriptQueue after each visit until told by the observer to stop.

As mentioned already, interacting with the ScriptQueue is mainly done through :ref:`LOVE ScriptQueue user interface <fig-scriptqueueui>`.
The interface allows users to stop, start and pause the ScriptQueue.
It also displays the status of scripts and any errors that occur.
Shuffling the order of queued scripts is also possible.
Lastly, users can quickly relaunch previously run scripts without having to re-enter any modifications to the default configuration.

.. figure:: /_static/ScriptQueueUI.png
   :name: fig-scriptqueueui
   :target: ../_images/ScriptQueueUI.png
   :alt: LOVE ScriptQueue user interface

   A screenshot of the LOVE interface to interact with the ScriptQueue.

In general, scripts require minimum interaction to be executed except, of course, for the occasional configuration.
A database of configuration will be available for users to execute the most common set of operations and they also have the possibility of editing configuration on the user interface, which also provides schema validation.

In general, using the ScriptQueue requires some familiarity with the observatory system and minimal set of software development skills.
Users should be able to, after inspecting the LOVE status screens, determine the state of the observatory and its readiness to perform certain types of operation.
Other than that, some knowledge about `yaml`_ and `json schema`_ may be useful for writing and inspecting script configuration, though :ref:`LOVE <fig-scriptqueueui>` will eventually provide features to help understanding and validating them prior to execution.

.. _yaml: https://yaml.org/spec/1.2/spec.html
.. _json schema: http://json-schema.org

.. _SAL_Scripts:

SAL Scripts
-----------

`SAL Scripts`_ are the files which contain the logic and coordination of events and CSCs that get executed by the `ScriptQueue`_.
It is not expected these will be modified during standard night-time operations.
All `SAL Scripts`_ vary greatly in complexity of the operation being performed.
These files have a strict format and must contain specific information in order to be capable of execution.
It is also possible to execute these from a Jupyter Notebook or from the command line when required.
This is particularly useful when actively developing or debugging a script.
For example, an example of a SAL script that performs a relatively fundamental task is `Enable MTCS <https://github.com/lsst-ts/ts_standardscripts/blob/develop/python/lsst/ts/standardscripts/maintel/enable_mtcs.py>`_ which brings all components of the TCS to the enabled state.
The script is normally a launched using the default configuration which enables all components, however, the flexibility is present to only enable a subset if the observer chooses to do so.
A more complicated script, such as `Prepare for On-Sky <https://github.com/lsst-ts/ts_standardscripts/blob/develop/python/lsst/ts/standardscripts/auxtel/prepare_for_onsky.py>`_ performs a series of order-specific operations to bring the systems online, then open the dome and telescope safely.

Regular operational scripts are separated into two distinct groups of `SAL Scripts`_; standard and external.

`Standard Scripts`_ hosts production-level operational scripts that are well tested and understood.
They must strictly follow the `development guidelines`_ and are subject to rigorous code review.

`External Scripts`_, on the other hand, acts as a staging or user sandbox area for the development of `SAL Scripts`_.
Following the `development guidelines`_ on this package is still recommended (but not as strictly enforced) and code is subjected to less rigorous code review.

Additional details about the classification of different levels of operations can be found in `tstn-010`_, as well as guidelines on how to contribute features to the code base.

.. _ScriptQueue: https://ts-scriptqueue.lsst.io
.. _SAL Scripts: https://ts-salobj.lsst.io/sal_scripts.html
.. _Standard Scripts: https://github.com/lsst-ts/ts_standardscripts
.. _External Scripts: https://github.com/lsst-ts/ts_standardscripts
.. _development guidelines: https://tssw-developer.lsst.io


.. _Jupyter-notebooks:

Jupyter notebooks
=================

The notebook server available at the summit control network is built on top of the `DM science platform`_, augmented with `Telescope and Site observatory control package`_.
They allow users to combine observatory control activities with data analysis in a highly interactive web-based interface.
This includes analysis of data from the EFD.

.. _nublado:
.. _DM science platform: https://nb.lsst.io
.. _Telescope and Site observatory control package: https://ts-observatory-control.lsst.io

It is important to emphasize that the notebook platform on the control network should be used mainly for activities that require controlling observatory components through the DDS middleware.
For pure data analysis activities, users should rely on other `nublado`_ instances (e.g. commissioning cluster, NCSA, etc.).

Although extremely powerful and flexible, we do not expect notebooks to be used on all situations.
These are the main situations where users are expected to resort to notebooks:

  - Executing an integration, commissioning or engineering activity that requires some level of interactivity.
    For instance, `determining the M1 Lookup-table for the Auxiliary Telescope Active Optics System <https://tstn-012.lsst.io/>`_
  - Executing a custom sequence of observations that require some level of interactivity, such as what was done to measure the `Sensitivity Matrix for the Auxiliary Telescope Active Optics System <https://tstn-016.lsst.io/>`_
  - Developing and testing new functionality not currently supported.
  - Debugging, testing and/or improving existing functionality.
  - Investigating issues with an individual component or a group of components.

In order to take full advantage of Jupyter notebooks users must acquire some familiarity with the observatory control system.
These are some basic concepts users should make an effort to be familiar with:

  - Commandable SAL Component (CSC).
  - `SalObj`_ Python library with special emphasis in the concept of a `Remote`_.
  - Some familiarity with the `Telescope and Site observatory control package`_.
  - Intermediate Python Skills.
  - Familiarity with `Python standard asyncio library`_.
  - Some familiarity with multithreading and coroutines.
  - Familiarity with git and GitHub.

As mentioned previously, any features developed in a notebook can be added to the production codebase following the procedure found in `tstn-010`_.

.. _SalObj: https://ts-salobj.lsst.io
.. _Remote: https://ts-salobj.lsst.io/py-api/lsst.ts.salobj.Remote.html#lsst.ts.salobj.Remote
.. _Python standard asyncio library: https://docs.python.org/3.7/library/asyncio.html

.. _Notebook-repository:

Notebook repository
-------------------

The main repository to store and manage Jupyter notebooks for interacting with the Rubin Observatory control system is `ts_notebooks`_.
Details on how this repository fits into the development process can be found in `tstn-010`_.

.. _ts_notebooks: https://github.com/lsst-ts/ts_notebooks
.. _tstn-010: https://tstn-010.lsst.io

.. _Actor-Expectations:

Expectations on Actor Interactions and Abilities
================================================

As discussed in the `Introduction`_, the roles of different personnel in the observatory will interact with the control system at multiple levels.
Some users will have a broad expanse of interactions, yet shallow in depth, whereas others will have narrow interactions but drill deep into the specific application.
It is useful to try to define these roles such that the user-experience and breadth of knowledge required to perform them can be better aligned to tailor the ease-of-use, flexibility, and functionality of the various interfaces.

.. _Actor-Definitions:

Definition of Roles
-------------------

For the purposes of this exercise, three different roles have been created.
Particularly early on, it is expected that many people will bridge two (or more) of these roles.
Below is a broad definition of these roles and how they differ in interaction and experience:

    1. Observing Specialists:
        These actors perform standard nighttime and daytime operations such as calibrations, start-up, shut-down, and monitoring.
        They are also required to troubleshoot issues that arise, but are expected to call specialists when unable to solve the issue in short order.
        These actors have a very broad knowledge of system operation but are not experts in a specific area, specifically software development.

    2. Commissioning Scientists:
        These actors are often focused to specific subsystems or characterization activities.
        Their level of knowledge is generally less expansive and more focused with an interest in driving deeper into the system functionality.
        Interactions with the system are often based upon performance analysis and understanding the coordination between specific subsystems.
        It is expected that these personnel will be performing activities that are not part of standard operation and therefore require greater flexibility.
        These actors have software development experience but are generally not significant contributors to the production code base.

    3. Software Developers
        These actors write the control system code that interacts with the components, often at numerous levels.
        Their level of knowledge is generally very deep in the area of the operation of a particular subsystem but their understanding of full system interaction and operation is much reduced compared to the other roles.
        These actors do not perform operational activities.
        Their software development expertise is very high and they are almost always writing production-level code.

.. _Actor-Interactions:

Actor Interactions with the Control Software
--------------------------------------------

Each actor is expected to interact with the software in different ways, but in nearly all cases users will use a blend of the tools presented previously.
This section defines the levels of control software interaction and knowledge required by each actor to perform their assumed tasks.
Note that it does not specify non-software tasks associated with someone in that typical position.


    - **Observing Specialists**

        - Conducts observatory functions primarily by using the LOVE interface to the scriptQueue

            - Includes opening, closing, taking manual images, performing calibrations, manual slewing, component state transitions

        - Monitoring of systems utilizes the LOVE interface
        - Launches scripts via the scriptQueue. Able to comfortably determine and modify the associated configuration parameters.
        - Troubleshooting of systems will utilize component EUIs and feedback presented from LOVE and/or the scriptQueue
        - Mining of information and analyzing of sequencing from the EFD is not required
        - Ability to enter and observe Chronograf dashboards is required
        - Possess ability to execute and make small edits to notebooks

            - Requires a minimal level of Python, and knowledge of few commands of git (e.g. git-checkout and git-pull)
            - Comfortable in finding and executing commands via high-level classes
        - Ability to update and maintain operations related documentation (written in rST, hosted on GitHub)

        - Some knowledge of low-level CSC functionality

            - Able to examine and change between configurations
            - Troubleshoot at the level diagnostics (error codes), status, and manual (non-DDS) motion where required

        - Interacts with data to perform offsetting, focus, but via tooling that provides the calculations
        - All information needed to operate the facility is provided to them, they are not required to build analysis and/or display tools

    - **Commissioning Scientist/Engineer**

        - Includes operator skills plus the following:
        - Extensive use of the notebook interface, including the writing of code and launching of scripts

            - Comfortable in Python, competent with git and GitHub

        - Ability to diagnose both system and component level behavioural issues
        - Not required to identify the issue in the source code
        - Able to create and load new config files
        - Writes and executes custom external scripts from both notebooks, ScriptQueue and LOVE
        - Not expected to write production level scripts (see tstn-010 for definition)
        - Able to switch between software versions of deployed components
        - Able to update scriptQueue container repos
        - Able to diagnose issues via the EFD/Chronograf
        - Ability to generate and maintain documentation (written in rST)
        - Works out of already defined environments (e.g. NTS or Summit)

            - Is expected to be comfortable changing between software packages in Nublado environment
        - Not expected to be familiar with software builds or deployment
        - Not expected to work with the standard development container/environment

    - **Software Developer**

        - Diagnoses behavioural issues at the code-level of CSCs or higher-level classes
        - Writes and reviews production level scripts

            - Expert in Python, git and other applicable languages

        - Modifies and builds components, tags for release where appropriate
        - Familiar with deployment strategies and restarting components (ArgoCD)
        - Ability to probe into individually deployed containers (Rancher)
        - Often works from the standardized development container



Examples of Different Levels of Operations
==========================================

This section includes various examples of procedures mentioned in the above sections.
Below is a series of tasks associated with a given actor.
In the case of operational examples (e.g. taking an image) they may include multiple possible procedures to perform the task with the goal of being able to demonstrate to the reader the advantages and disadvantages of each system.
In the case of taking an image, this can be done from the scriptQueue via LOVE, by launching a script from a notebook, or just from a command in a notebook.
From the example it is clear that launching a script from a notebook to take a simple image is onerous and not the recommended approach.

Operational Tasks:

    - Slewing
    - Offsetting
    - Taking an Image
    - Launching a ccript and editing the configuration in LOVE
    - Execution of a notebook


Commissioning Actor Tasks:

    - Updating a CSC configuration file
    - Creation of a notebook to be used for testing
    - Example of how to write a script


Items to be addressed in a future revision
==========================================

    - On-the-fly image interaction
    - Communication/coordination with other software systems

        - OCPS? Other tools?
        - Logging?

    - Discussion of test-stands and how to use them


.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
