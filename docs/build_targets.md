# Build Targets

Ystari will target .NET Standard 2.0. Consult [this page](https://github.com/dotnet/standard/blob/master/docs/versions.md) for information on which frameworks will support .NET Standard 2.0. We also plan on targeting modern UI frameworks like XAML and (hopefully!) WebAssembly. Note that right now Ystari is targeting 1.6 because there is no 2.0 tooling available, but that should be coming in 2017.

Ystari will also be built using C#7.

**RDL:** Starting in 2018 CSLA was finally able to drop all targets except for NS2. With the exception that legacy support for .NET 4, 4.5, and 4.6 remains in place. Really though, the codebase targets NS2 for all intents and purposes.
