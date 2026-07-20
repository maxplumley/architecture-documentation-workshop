# Architecture Documentation Workshop

This workshop introduces a methodology for documenting software architectures by leveraging arc42 and C4 Model.

Specifically, it covers:

- why we need a methodology for authoring documentation
- an introduction to the arc42 framework for software architecture
- an introduction to the C4 Model approach to software architecture diagramming
- a model-based diagrams-as-code workflow for generating C4 diagrams with LIKEC4

## Why do we need a methodology?

Quality documentation is a critical, and often under-prioritized, non-function requirement of any software system. Poor (or non-existent!) documentation adversely impacts a team's ability to effectively evolve a software system, engage external stakeholders, or onboard new members.

As developers embarking on documenting a software system, we're often tripped up because it's:

- not obvious *what* needs to be communicated in the documentation.
- nor is it clear *how* to best communicate *what* needs to be communicated.

This is entirely understandable - as developers, we tend to prioritize building deep expertise delivering working, performant software over communication skills. Even with the best intentions, it's easy to fall into *anti-patterns* that rely on internal mental models of what quality documentation 'is like' (rather sophisticated, tested, models that capture what quality actually 'is'), to produce the kind of 'developer prose' that results in unreadable, ineffective project wikis. Thankfully there are many resources out there that take the guess-work out of authoring quality documentation. This workshop aims to introduce you to two such resources - arc42 and C4 Model.

> arc42 and C4 provide frameworks that assist us to systematically and repeatably apply principals of technical documentation, if you're interested in these underlying principals consider this [30 minute read](https://www.innoq.com/en/blog/2022/01/principles-of-technical-documentation/)

## The arc42 framework

The [arc42](https://arc42.org/) framework provides a lightweight approach to software architecture documentation. arc42 outlines *what* is best to document and *how* we should do it; allowing us to focus on the task of documenting with confidence. Specifically, arc42 outlines how to prepare documentation that answers questions such as:

- *why* does this system exist?
- *what* components collaborate to implement feature A?
- *how* is this system deployed?
- *when* transaction B fails what kind of compensation is executed?
- as an integrating system, *where* can I find a specification to consume this system's API?
- *when* did we decide to store system data in product C, *who* decided this, and are the forces that drove that decision still relevant?

You will notice that these questions could be quite difficult to answer by simply interrogating the system's source code or version control history - and it would be especially challenging to answer many of these for large complicated systems (which may be implemented across multiple code bases, each with their own version control history) without additional context. Typically, it's impossible for external stakeholders to find the answers they require without quality documentation that captures the wider context in which a system exists. Internal team members may have better luck, but will also struggle if the question is concerned with a part of the system unfamiliar to them. In such scenarios the solution - in the absence of quality documentation - is often to 'find the person who knows'; however, this strategy exposes any serious project to significant risk: what if this 'person who knows' moves out of the team and is no longer available, or simply forgets the critical context required?

The arc42 template details 12 sections that have been developed to provide the maximum critical context to safeguard a project for the least amount of effort. Details regarding each section can be found on the arc42 website, but we will focus on just three in this subject:

- **Context and Scope**: this section documents the wider context in which the software system exists, it identifies the external users and systems that use or integrate with the system.
- **Building Block View**: this section documents the static decomposition of the system, it captures how code is organized into components and the dependencies between these component parts.
- **Runtime View**: this section captures the dynamic behavior of the running system, it details how important features work, how data flows through the system, and how exceptional behavior is handled.

## C4 diagrams

Just as arc42 provides an approach for structuring our documentation, the [C4 Model](https://c4model.com) provides an approach for generating high-quality software architecture diagrams. The C4 Model was developed as an 'unofficial' evolution of UML, at a time where teams were abandoning heavy upfront design practices demanded by UML in favour of Agile and just-in-time design. Ultimately C4 tries to reinstate 'just enough' structure and syntax to realize some of the great communication benefits that a shared understanding of UML provided, while remaining light-weight enough to be compatible with Agile ways of working.

The C4 Model provides a set of hierarchical abstractions paired with hierarchical diagrams that capture the static decomposition of a software system from four levels of granularity.

| abstraction | diagram | description | analogy |
| =========== | ======= | =========== | ======= |
| Software System | System Context | 

## Model-based diagrams-as-code with LIKEC4

Diagrams-as-code is an approach to generating software diagrams from code-like markup instead of manually crafting diagrams in a visual editor. The key benefits of a diagrams-as-code approach are:

- because the diagrams are code, they can be tracked in version control just like any other code source.
- styling and placement of diagram elements is handled by an engine, meaning that we don't have to worry about placing elements in the first place or re-arranging them when we make updates.
- because the diagrams are code we can easily build tooling that emits or processes this code as part of an automated build pipeline.

If you've ever used tools like PlantUML or Mermaid you will be familiar with the concept of diagrams-as-code, and perhaps have experienced the benefits outlined previously first-hand. Tools like PlantUML and Mermaid are excellent for quickly generating small one-off diagrams, but they  is that they Tools like LIKEC4 take a 

Model-based approaches provide:

- a single source of truth from which diagrams can be generated in a consistent way.
- the ability to declare and generate multiple 'views' of a system as a series of diagrams, which is particularly helpful for managing the complexity of documenting large systems.
- greater tooling opportunities that emit or process the model, to enforce constraints or enrich the model with meta data to improve meaning.

LIKEC4 provides a syntax and tooling (via the LIKEC4 CLI) to declare the C4 Model hierachchical abstrations in code and from this generate the corresponding C4 Model diagrams.

The remainder of this workshop shows you how to leverage the C4 Model (and LIKEC4) to document a hypothetical Agent as a Service software system:  

> The Agent as a Service (AaaS) system provides users the ability to configure AI agents to act on their behalf in a secure environment. Users consume AaaS services via two interfaces, the AI Experience (AIX) web UI (maintained by an external team), or directly via a RESTfull AI Agent Harness API exposed by the AaaS - AIX consumes AaaS via this same REST API. The AaaS is currently integrated with two LLM providers, OpenAI and Z.ai. The AaaS is deployed as four applications; an Envoy Proxy that acts as a gateway to the AaaS system, an Agent Harness application that exposes the AI Agent Harness API and implements all agent logic; an LLM Provider application that routes requests from the Agent Harness to LLM services provided by OpenAI and Z.ai; a PostgreSQL database in which all AaaS data is stored.

### aaas.c4

We begin by declaring a LIKEC4 specification for the C4 Model hierarchy in a `aaac.c4` file. The C4 hierarchy is comprised of software systems, containers, components and code, however we will not be documenting code so we'll leave this level of the hierarchy out of our specification. To model this hierarchy we'll need to include a LIKEC4 element for each level:

```likec4
specification {
    element actor {
        notation 'Actor'
        style {
            shape person
        }
    }
    element software-system {
        notation 'Software System'
        style {
            color green
        }
    }
    element container {
        notation 'Container'
    }
    element component {
        notation 'Component'
        style {
            shape component
        }
    }
}
```

While actors are not a layer of the C4 Model it is critical that we can capture system users. We'll add an actor element for this purpose:

```likec4
specification {
    element actor {
        notation 'Actor'
        style {
            shape person
        }
    }

    // other elements...
}
```

Now we can begin modeling the software system level of the AaaS system. From the system description we know that the external consumers of the AaaS system are a single User actor and the AIX web UI, and the external systems on which AaaS depends are the LLM services provided by OpenAI and Z.ai. We can model these systems as so:

```likec4
specification {
    // elements...
}

model {
    user = actor 'User'
    oai = software-system 'OpenAI' {
        style {
            icon tech:openai
        }
    }
    zai = software-system 'Z.ai'
    aix = software-system 'AIX'
    aaas = software-system 'AaaS'

    aix -> aaas 'Consumes AI Agent Harness API' {
        technology 'HTTPS'
    }
    aaas -> oai 'Accesses services provided by'  {
        technology 'HTTPS'
    }
    aaas -> zai 'Accesses services provided by'  {
        technology 'HTTPS'
    }
    user -> aix 'Uses in browser'
    user -> aaas 'Uses in automation'  {
        technology 'HTTPS'
    }
}
```

We now have both a specification and model, which is enough to begin generating diagrams.

> The LIKEC4 tooling demonstrated in this workshop requires that you have NodeJS installed, if you don't have NodeJS installed install the latest version from the [official NodeJS website](https://nodejs.org/en/download).

LIKEC4 provides a number of ways to generate diagrams from `.c4` files, and the first we'll look at is the LIKEC4 server which can generate a live previews giving us instantaneous feedback as we modify our model. Start the server with the `LIKEC4` CLI:

```shell
npx likec4 start
```

On starting the server the LIKEC4 UI should be launched in your default browser. Notice that there is a single diagram titled 'Landscape view', click on this diagram to view a C4 system context diagram generated from our model. To see the live preview in action, change the color of actors to red by modifying the actor element styling

```likec4
specification {
    element actor {
        notation 'Actor'
        style {
            shape person
            color red
        }
    }

    // other elements...
}

model {
    // software systems
}
```

This is an excellent start, but we can generate far more views than just the C4 system context. Let's model out the containers that comprise the AaaS system and generate a C4 container diagram. Add the following containers to the `aaas` software system:

```likec4
specification {
    // elements...
}

model {
    // other software systems...

    aaas = software-system 'AaaS' {
        container gw 'API Gateway' {
            style {
                icon tech:envoy
            }
        }
        container ah 'Agent Harness'
        container lp 'LLM Provider'
        container ad 'AaaS Datastore' {
            style {
                shape storage
                icon tech:postgresql
            }
        }

        gw -> ah 'Forwards requests to' {
            technology 'HTTPS'
        }
        ah -> lp 'Accesses models via' {
            technology 'HTTPS'
        }
        ah -> ad 'Stores data in' {
            technology 'HTTPS'
        }
    }

    // other software systems...
}
```

You will notice that adding these containers to the model has no effect on the generated system context diagram. This is expected, the default view that generates this system context diagram only includes elements at the top level of the model, our freshly declared containers are a level too deep. To capture these containers in a C4 container diagram we will need to declare an new LIKEC4 view:

```likec4
specification {
    // elements...
}

model {
    // software systems...
}

views {
    view aaas-container {
        include 
            aaas.*,
            aaas.* -> *,
            * -> aaas.*
    }
}
```
