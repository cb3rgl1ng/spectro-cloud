# Research

- https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-
- https://kubernetes.io/docs/concepts/overview/
- https://kubernetes.io/docs/reference/kubectl/
- https://docs.spectrocloud.com/clusters/cluster-management/palette-webctl/
- https://kubernetes.io/docs/tasks/debug/debug-cluster/kubectl-node-debug/
- https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/
- https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/
- https://kubernetes.io/docs/reference/kubectl/generated/kubectl_exec/
- https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/

# Outline

- tool: kubectl
- concept: kubectl commands
- commands
    - get pods
    - exec
    - logs
    - debug

# Feedback 

## Wolbrink
- Maybe expand it with example commands and command output? I'd picture that as a new section between the table and references.I also wondered about having a 4th column on the table for short example commands too but a section would let you include samples like they indicated.
- Your first description cell uses periods.  The other 3 do not. It also seems like those 4 description cells use a slightly different style.  2&3 are very close to each other...and maybe 1 and 4 as well?

## Friedman
- There's content nuance I'm not familiar with, like mentioning Azure. Sounds like an "example" but not sure if it should be in your doc or not
- Would it be useful to add "example response"? That may just be me being verbose.

## Archibald
- Title is a bit vague as it doesn't contain (even yet) because it's certain aspects of debugging. Perhaps something like "debugging workloads in kubernetes" or "debug commands for kubernetes workloads"
- It's weird to me personally to use a command as a title without somehow denoting it as such - a la 'get pods' and 'logs'  - perhaps put them in quotes or italics or use plain english for the titles and put the commands in a subtitle (this could just be me)
- A small section explaining difference between pods and containers is probably helpful for a "basic knowledge of kubernetes" especially in the context of interacting with pods primarily through k8s commands to then get into a pod to do things with a specific container
- might be worth mentioning that a functional cli is necessary for everything that follows with a link to the documentation for that before moving forward
- each section seems to follow a 'command' -> summary -> command string -> response -> closing thoughts which is a good flow but it's hard to identify it until you read it a couple times. As an example, the final 'debug' section's response section is half white and half blue and there are no closing thoughts
- For the references section, I'd like to have high level documentation and then drop links to specific articles or subsections therein inline with the commands.  So get, logs, exec, and debug should be inline with a reference link at the bottom to the whole 'commands' documentation
- under exec (and just in general) I think dropping in a couple example commands is helpful.  In this context, ps, top, cat, etc could be given as examples. - I'd also put a note here denoting that fucking with anything in prod should be done very carefully.
- under exec, I'd rephrase it to say something like "Next, use exec to run commands where possible inside a pod and/or a container within it.  Examples here include 'top', 'df', or opening a shell for further interaction.  NOTE: Always note the environment as executing commands in a pod or container in production could have unintended consequences - always follow change management protocols."
- Does that command in the exec section result in the database output in the response or are we assuming the user just opened the shell to run them?
