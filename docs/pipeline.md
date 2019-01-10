# Pipeline

{TODO: Discuss what the Ystari pipeline should be. Meaning, how will the logical and physical servers be handled/resolved, what will be done to accomodate any context values as we'd like to get away from `ApplicationContext`, what methods should be called in what order, etc.}

**RDL:** As of version 4.9 CSLA's config system supports both `System.Configuration` and `Microsoft.Extensions.Configuration`. The values do all funnel into a static `AppSettings` dictionary and they are still exposed by `ApplicationContext`. However, this was a major step toward an abstraction that could allow CSLA itself to stop using `ApplicationContext`, and instead use the new model we created to mitigate differences between the old .NET and new .NET Core config subsystems.
