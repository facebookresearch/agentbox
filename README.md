# AgentBox

> [!WARNING]
> AgentBox is under construction. APIs, behavior, and documentation may
> change without notice.

AgentBox is a distributed container orchestrator for AI agents. It is built for
researchers studying multi-agent systems on high-performance computing (HPC)
clusters without requiring root access. It was originally built at Meta FAIR to [study research agents](https://arxiv.org/abs/2603.26499) and has since
been refactored and improved. AgentBox uses
[Apptainer](https://apptainer.org/) to isolate agents from each other and from accessing privileged test sets. AgentBox leans on
[Monarch](https://github.com/meta-pytorch/monarch) to coordinate agent containers.

> **Supported platforms:** Linux (local and [Slurm](https://slurm.schedmd.com/overview.html)). macOS works for local
> workers; containers on macOS run through a
> [Lima-backed Apptainer](docs/installation.md#macos). Windows is not
> supported.

## Quick start

Requires Python 3.12+. Containers additionally need `apptainer` on the worker.

```bash
pip install -e .
```

```python
from agentbox import AgentBoxManager, Container

with AgentBoxManager.create_local(num_workers=1) as manager:
    with manager.acquire_machine() as machine:
        print(machine.run("uname -a").stdout)  # on the worker host

        with Container(machine, image="docker://python:3.12-slim") as box:
            result = box.run(["python3", "-c", "print('hello from a container')"])
            print(result.stdout)
```

To run on Slurm instead, replace the manager:

```python
from agentbox import AgentBoxManager, SlurmAllocation

manager = AgentBoxManager.create_slurm(
    allocation=SlurmAllocation(partition="cpu", cpus_per_task=8),
    num_workers=16,
)
manager.wait_for_workers(min_available=16)
```

## Documentation

- [Installation](docs/installation.md)
- [Core concepts](docs/concepts.md)
- [Containers](docs/containers.md)
- [API map](docs/api.md)
- [Examples](docs/examples.md)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) and our
[Code of Conduct](CODE_OF_CONDUCT.md).

## License

AgentBox is Apache 2.0 licensed, as found in the [LICENSE](LICENSE) file.

## Citation
If you use this library in your work, please cite the following paper:

```
@article{hambardzumyan2026aira_2,
  title={AIRA\_2: Overcoming Bottlenecks in AI Research Agents},
  author={Hambardzumyan, Karen and Baldwin, Nicolas and Toledo, Edan and Hazra, Rishi and Kuchnik, Michael and Omari, Bassel Al and Foster, Thomas Simon and Protopopov, Anton and Gagnon-Audet, Jean-Christophe and Mediratta, Ishita and others},
  journal={arXiv preprint arXiv:2603.26499},
  year={2026}
}
```
