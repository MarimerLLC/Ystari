# Stereotypes

We anticipate that the stereotypes that currently exist in CSLA would also exist in Ystari, though not all of them. For example, the "binding list" types (like `DynamicBindingListBase`) would not be carried over as they only have relevance in WinForms applications. We are considering name changes like "Domain" instead of "Business" so we'd have stereotype classes like:

* `EditableDomainBase`
* `ReadOnlyDomainBase`
* `EditableDomainListBase`
* `ReadOnlyDomainListBase`
* `CommandBase`

Note also that `CriteriaBase` would not be included as there would be no need for that type either.

The metastate for the stereotypes (like `IsSavable`) would also remain, though `IsDirty` would be changed to `IsChanged`.