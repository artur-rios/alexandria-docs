---
title: About Alexandria
linkTitle: About
menu:
  main:
    weight: 40
---

{{< blocks/cover title="About Alexandria" height="auto" color="primary" >}}
A personal library, built from the files you already have.
{{< /blocks/cover >}}

{{% blocks/lead %}}
Alexandria started from a simple frustration: a hard drive full of films,
books, and comics, and no single place to see any of it.

It is deliberately small in scope. One person, one machine, one library. No
cloud, no accounts to manage, no synchronisation, and no attempt to become a
media server. It reads what is already on your disk, records what it finds, and
gets out of the way.

Music is not part of it: that lives in Orpheus, a separate project.
{{% /blocks/lead %}}

{{% blocks/section color="dark" type="row" %}}

{{% blocks/feature icon="fa-brands fa-rust" title="Rust core" url="https://github.com/artur-rios/alexandria-api" %}}
The domain library, the REST/JSON server, and the C ABI.
{{% /blocks/feature %}}

{{% blocks/feature icon="fa-solid fa-desktop" title="Flutter desktop" url="https://github.com/artur-rios/alexandria-ui" %}}
The application, for Windows and Ubuntu.
{{% /blocks/feature %}}

{{% blocks/feature icon="fa-solid fa-scale-balanced" title="Open source" url="https://github.com/artur-rios/alexandria-api/blob/main/LICENSE" %}}
Free and open source under the Artur Rios Attribution License: use, fork,
modify and extend it, crediting the author. Bundled third-party libraries keep
their own licences.
{{% /blocks/feature %}}

{{% /blocks/section %}}
