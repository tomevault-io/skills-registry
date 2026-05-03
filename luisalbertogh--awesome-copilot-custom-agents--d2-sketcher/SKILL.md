---
name: d2-sketcher
description: Guidelines to define technical diagrams in D2 with the desired settings and preferences. Use it when creating D2 diagrams for any technical topic (sequence diagrams, class diagrams, cloud architecture diagrams, etc). Use when this capability is needed.
metadata:
  author: luisalbertogh
---

# D2 Sketcher

You are an expert in creating technical diagrams using D2 syntax. Your role is to help define and generate D2 diagrams based on specific requirements and preferences.

## When to Use This Skill

Use this skill whenever you need to create technical diagrams in D2 format, including but not limited to:

- System architecture diagrams
- Sequence diagrams
- Class diagrams
- Cloud infrastructure diagrams
- Data flow diagrams

**Do not use this skill** if the diagrams need to be created using a different tool or format that it is not D2.

## Prerequisites

The below prerequisites must be met before attempting the creation or update of the D2 diagrams:

- Do not check for D2 binaries or installation. That is out of the scope of this skill.
- Use the icons located under the `icons` folder. Adjust the relative path to that folder from the location where the D2 diagrams are saved.
- Ensure to read the content under the `references` folder for further usage as indicated in this skill.
- Do not use D2 syntax that is not valid for the used version. Print out the installed D2 version if needed by running the `d2 --version` command.

## Guidelines

The rules defined from this section must be applied when developing D2 diagrams.

### Shapes

Apply these rules for the definition of `shapes`:

- When defining shapes, do it in separate blocks:

    ```text
    # Good example
    one: {
        shape: image
        label: One
    }

    other: {
        label: Other
    }

    # Bad example
    SQLite; Cassandra
    ```

- Use always a `label` as part of the shape definition.

Use the content of the web page in `https://d2lang.com/tour/shapes/` as a reference.

#### Images in shapes

Use an image for the shape only under the following circumstances:

- Based on the item that will be represented with the shape, look into [this table](./references/image-ref.md) and check using the `name` column, if there is some image available for that item:

  For example, for an `AWS Internet Gateway`, there is a row in the table with name equal to `internet_gw` pointing to an available image in `../icons/aws/internet_gw.svg`

  If there are multiple image formats for the same shape type, use `svg` over any other format.

  Use the value in the `path` column to apply the image onto the corresponding shape. Do it as especified in the next bullet points.

- If the shape does contain more inner shapes, use the `icon` attribute but do not set `shape: image`.

  ```text
  # For example
  aws-region: {
      label: AWS Region
      icon: ../icons/aws/aws_region.svg
      style: {
          font-size: 55
      }

      dagster-cluster: {
          label: bay-cdp-aurora-cluster
          icon: ../icons/aws/ecs.svg
          style: {
              font-size: 45
          }
      ...
      }
  }
  ```

- If the shape does not contain other inner shapes, use the `icon` attribute and do set `shape: image`.

  ```text
  # For example
  dagster-cluster: {
      label: bay-cdp-aurora-cluster
      icon: ../icons/aws/ecs.svg
      style: {
          font-size: 45
      }

      dagster-agent: {
          shape: image
          icon: ../icons/aws/ecs_service.svg
          label: dagster-hybrid-agent
          style: {
              font-size: 25
          }
      }
  }
  ```

- If no proper image is found, ignore the attribute `shape` and the attribute `icon` and do not use any `image` for that shape.

**IMPORTANT** - The path to the `icon` attribute must be relative respect of the folder where the diagrams are saved.

### Connections

Apply these rules for the definition of `connections`:

- Use always directional connections (`->`, `<-` or `<->` for bidirectionals).
- Label the connections: `Read Replica 1 -- Read Replica 2: Kept in sync`.
- If possible, try to group as many connections as possible in the same line:

    ```text
    # Good example
    A -> B <- C: Conn 1

    # Bad example
    A -> B: Conn 1
    C -> B: Conn 1    
    ```

- Use `font-size` style for the connections with a minimum value of `35`:

    ```text
    canvas2.github.dagster-repo -> canvas2.cdp-tooling.ecr-repos: push {
        style {
            font-size: 35
        }
    }
    ```

- When defining the *connection*, ensure the endpoint names for the connection are properly composed if the shapes are placed inside containers:

    ```text
    # Good composition
    container: {
        label: "Serverless File Processing Application"
        icon: ../icons/aws/aws_icon.svg
        style: {font-size: 55}

        shape01: {
            label: "S3 Event Trigger"
            icon: ../icons/aws/s3_bucket.svg
            style: {font-size: 45}
        }
        shape02: {
            label: "Lambda Processor"
            icon: ../icons/aws/lambda.svg
            style: {font-size: 45}
        }
    }

    # Connections
    container.shape01 -> container.shape02: "Triggers" {style: {font-size: 35}}
    ```

Use the content of the web page in `https://d2lang.com/tour/connections/` as a reference.

### Containers

When defining `containers`, apply these rules:

- Use nested containers to represent hierarchical relationships.
- Label each container clearly.
- Use containers to group related components or systems.
- Keep the same styles for the containers except for the font-size that must be reduced progresively for inner containers:

    ```text
    # Good example
    aws-region: {
        label: AWS Region
        icon: ../icons/aws/aws_region.svg
        style: {
            font-size: 55
        }

        dagster-cluster: {
            label: bay-cdp-aurora-cluster
            icon: ../icons/aws/ecs.svg
            style: {
                font-size: 45
            }

            dagster-agent: {
                shape: image
                icon: ../icons/aws/ecs_service.svg
                label: dagster-hybrid-agent
                style: {
                    font-size: 25
                }
            }
        }
    }
    ```

- Use attributes like `grid-columns`, `grid-rows`, `horizontal-gap` and `vertical-gap` to lay out the content of the containers in the most proper way.

Use the content of the web page in `https://d2lang.com/tour/containers/` as a reference.

#### Examples of grid diagrams

Use parent containers without label and opacity set to 0 to lay out inner containers:

```text
canvas1: {
    label: ""
    grid-rows: 4
    style: {
        opacity: 0
    }

    container1: {
        ...
    }

    container2: {
        ...
    }

    container3: {
        ...
    }

    container4: {
        ...
    }
}
```

Use the below tricks to create *invisible spans* that can be used to organize the components of the diagram for better representation:

```text
# Invisible span
classes: {
  invisible: {
    style.opacity: 0
    label: a
  }
}

canvas1: {
    label: ""
    grid-rows: 4
    style: {
        opacity: 0
    }

    container1: {
        ...
    }

    pad1.class: invisible

    container3: {
        ...
    }

    container4: {
        ...
    }
}
```

Use the content of the web page in `https://d2lang.com/tour/grid-diagrams/` as a reference.

### Variables and globs

Use variables and globs when they bring some clear benefit, for example to apply common settings to multiple items like containers or shapes:

```text
# Example for variables
direction: right
vars: {
  server-name: Cat
}

server1: ${server-name}-1
server2: ${server-name}-2

server1 <-> server2

# Example for globs
iphone 10
iphone 11 mini
iphone 11 pro
iphone 12 mini

*.height: 300
*.width: 140
*mini.height: 200
*pro.height: 400
```

Use the content of the web pages in `https://d2lang.com/tour/vars/` and `https://d2lang.com/tour/globs/` as references for variables and globs respectively.

### Comments

Use comments to explain complex parts of the diagram or to provide context:

```text
# Comments start with a hash character and continue until the next newline or EOF.
x -> y

"""
This is also a valid
block comment
"""

// This is NOT a valid comment in D2 syntax
```

Use the content of the web page in `https://d2lang.com/tour/comments/` as a reference.

**COMPULSORY**: Use a comment for each new shape and connection at least.

### Legends

Use legends if they provide useful information and they do not spoil the composition of the overall diagram:

```text
vars: {
  d2-legend: {
    a: {
      label: Microservice
    }
    b: Database {
      shape: cylinder
      style.stroke-dash: 2
    }
    a <-> b: Good relationship {
      style.stroke: red
      style.stroke-dash: 2
      style.stroke-width: 1
    }
    a -> b: Bad relationship
    a -> b: Tenuous {
      target-arrowhead.shape: circle
    }
  }
}

api-1
api-2

api-1 -> postgres
api-2 -> postgres

postgres: {
  shape: cylinder
}
postgres -> external: {
  style.stroke: black
}

api-1 <-> api-2: {
  style.stroke: red
  style.stroke-dash: 2
}
api-1 -> api-3: {
  target-arrowhead.shape: circle
}
```

Use the content of the web page in `https://d2lang.com/tour/legend/` as a reference.

### Composition

**Do not compose** multiple boards into a single diagram unless explicitely requested.

## References

Use the below references as valid examples on how to define D2 diagrams for multiple use cases.

### Web pages

Use the content of the web pages from the below list as valid references and examples for multipurpose features when creating D2 diagrams:

- [Simple diagram](https://github.com/terrastruct/d2/blob/master/docs/examples/chess/dia.d2)
- [Complex system architecture](https://github.com/terrastruct/d2/blob/master/docs/examples/twitter/in.d2)

### Local guides and examples

- [D2 samples](references/d2-samples.md). More valid examples fitting into different use cases. Review all the cases and **select those more suitable for the scenario to depict**.

## Generate image files

When generating an image file for each diagram, use the `SVG` format by default. Run the following command from the terminal:

```bash
d2 --layout=elk path/to/diagram.d2
```

> **IMPORTANT:** Do not use any additional flag or parameter, besides the one specified above.

## In sumary

When creating D2 diagrams:

- Always follow the defined guidelines for shapes, connections, containers, variables, comments, and legends.
- Use the provided references as examples to ensure clarity, consistency, and effectiveness in your diagrams.
- Try to use the images found under `icons` and the corresponding subfolders when defining shapes.
- Ensure that diagrams are easy to understand and visually appealing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luisalbertogh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
