UWP Resources Issue repro readme
================================

# What is the issue

UWP Apps don't include resources from *external assemblies* (such as Windows Community Toolkit) anymore and
can't load any resources causing controls not to show up (missing default control templates).

# How to reproduce

Simply add multiple elements of ;pdb to the project file. Then resources will stop working without
any form of warning and/or error.

In my case, it was because I added this in `Directory.build.props`:

```
	  <PropertyGroup>
	    <AllowedOutputExtensionsInPackageBuildOutputFolder>$(AllowedOutputExtensionsInPackageBuildOutputFolder);.pdb;.xml</AllowedOutputExtensionsInPackageBuildOutputFolder>
	  </PropertyGroup>
	
	  <!-- (temporary?) workaround for https://github.com/dotnet/sdk/issues/1458, but note that this breaks resources in UWP projects -->
	  <ItemGroup>
	    <!-- 
	        Disabled for now since portable PDB are disabled as well since they don't work for the full .NET Framework,
	        consider moving this to explicit with the right targets. Note that it doesn't do anything else than allowing
	        pdb files to be included in AllowedOutputExtensionsInPackageBuildOutputFolder, which is already done above
	     -->
	    <PackageReference Include="SourceLink.Copy.PdbFiles" Version="2.8.3" PrivateAssets="All" />
	  </ItemGroup>
```

After days of investigations, I finally figured out it was caused by this line in the generic (shared) `Directory.build.props` file
we use for all our projects.

# Running the repro

1) Simply start the repro, and the text should show *no shadow* (so the bug is present)

![](images\1_repro.png)

2) Comment or remove the package reference to `<PackageReference Include="SourceLink.Copy.PdbFiles" Version="2.8.3" PrivateAssets="All" />`

3) Run the repro again, text now has shadow

![](images\2_fixed.png)