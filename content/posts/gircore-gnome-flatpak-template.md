---
title: "A Template for Building .NET GNOME Applications with Flatpak"
date: 2025-09-25T11:50:38+02:00
draft: false
tags: .net, gnome, flatpak, template, gircore, dotnet
description: "Introducing TenderOwl.GirCoreTemplate: a project template that automates creating applications using **Gir.Core** bindings, the modern **Blueprint** interface markup language, and **Flatpak** packaging."

---

## Introduction

Developing native applications for the GNOME platform using .NET has become significantly easier. I'm excited to introduce **TenderOwl.GirCoreTemplate.CSharp**, a project template that automates creating applications using **Gir.Core** bindings, the modern **Blueprint** interface markup language, and **Flatpak** packaging.

My journey with C# began back in 2003, and when the Gir.Core project emerged—a binding generator for GTK4 and GObject-based libraries—I couldn't resist getting involved. Over time, I developed the idea to create a template that would streamline getting started with these technologies while enabling the use of familiar tools like GNOME Builder. This template is the result of that work.

## Prerequisites

Before getting started, ensure you have the following software installed:

- [**.NET SDK 9.0**](https://dotnet.microsoft.com/) or newer
- [**Blueprint Compiler**](https://gnome.pages.gitlab.gnome.org/blueprint-compiler/)
- [**Flatpak**](https://flatpak.org/) with GNOME runtime (for building and testing)
- [**GNOME Builder**](https://apps.gnome.org/Builder/) (recommended for development)

## Template Installation and Project Creation

### Installing the Template

The template is available via NuGet and can be installed with a single command:

```bash
dotnet new install TenderOwl.GirCoreTemplate.CSharp
```

### Creating a New Project

Create a project from the template by specifying a unique application ID (replace `YOUR_APP_ID` with your identifier, such as `io.github.yourname.MyApp`):

```bash
dotnet new gnome-gircore --app-id YOUR_APP_ID -n MyGnomeApp -o MyGnomeApp
```

> **Important:** The application ID must be unique and follow the [Reverse DNS standard](https://docs.flatpak.org/en/latest/conventions.html#application-ids).

## Project Structure

After creating the project, you'll see the following file structure:

```
your-application/
├── MyApp/
│   ├── Program.cs                     # Application entry point
│   ├── Constants.cs                   # Constants including application ID
│   └── ui/                            # User interface classes
│       └── MainWindow.cs              # Main application window
├── data/                              # Data and resource files
│   ├── icons/                         # Application icons
│   ├── ui/                            # Blueprint files
│   │   └── MainWindow.blp             # Main window layout
│   ├── __APP_ID__.desktop             # Desktop integration file
│   ├── __APP_ID__.appdata.xml         # Application metadata
│   ├── __APP_ID__.gresource.xml       # Resource definition file
│   └── __APP_ID__.gschema.xml         # GSettings schema definition
├── build-aux/                         # Build auxiliary files
│   ├── __APP_ID__.yaml                # Flatpak manifest
│   └── nuget-sources.json             # NuGet sources configuration
└── build/                             # Generated files (created during build)
```

### Key Project Files

- **`Constants.cs`** — Contains application constants including `APP_ID`. When changing the ID, update it in all files using `__APP_ID__`.
- **`build-aux/__APP_ID__.yaml`** — Flatpak manifest defining dependencies, permissions, and build stages.
- **`data/ui/MainWindow.blp`** and **`ui/MainWindow.cs`** — UI layout and application logic pairing.
- **`__APP_ID__.appdata.xml`** — Metadata for GNOME Software Center.
- **`__APP_ID__.gschema.xml`** — Application settings schema.
- **`__APP_ID__.gresource.xml`** — Application resource definition.

## Building and Running

### Development with GNOME Builder (Recommended)

1. Open the project folder in GNOME Builder
2. The IDE will automatically recognize the project
3. Click "Run" to build and launch the application in the required environment

### Building with flatpak-builder

To build and install the application manually, run these commands from the project root directory (replacing `__APP_ID__` with your identifier):

```bash
flatpak-builder --user --install build-dir build-aux/__APP_ID__.yaml --force-clean
```

After installation, the application will appear in your menu and can be launched with:

```bash
flatpak run YOUR_APP_ID
```

> **Important:** Running via `dotnet run` won't work as the application depends on GNOME-specific features (desktop files, GSettings, resources, etc.).

## Build Process

The build process is detailed in the application's Flatpak manifest (`build-aux/__APP_ID__.yaml`):

1. Blueprint Compiler installation
2. .NET SDK and runtime setup
3. Application build including:
   - C# code compilation
   - Blueprint file compilation into UI resources
   - Resource compilation using `gnome-compile-resources`
   - Application metadata copying
   - GSettings schema compilation

> **Note:** Any additional files required for your application must be manually added to the Flatpak manifest.

## Working with Blueprint Files

Blueprint is a modern replacement for traditional XML/.ui files for describing interfaces in GNOME. When working with them:

- Files must be located in the `data/ui/` directory
- Use the `.blp` extension—files with other extensions are ignored by the build system
- For example, `MainWindow.blp` will be processed, while `MainWindow.foo` will be ignored

## Next Steps

After creating your project, you can:

1. **Modify the interface** — Edit files in the `data/ui/` directory. Find official Blueprint documentation on the [project website](https://jwestman.pages.gitlab.gnome.org/blueprint-compiler/).
2. **Add logic** — Work with code in the `ui/` directory. Study the [Gir.Core documentation](https://github.com/gircore/gir.core) to understand available GTK APIs.
3. **Configure the manifest** — Open `build-aux/__APP_ID__.yaml` to add your application's dependencies (e.g., for network or database functionality).
4. **Add icons** — Place your icons in the `data/icons/` directory in appropriate sizes.

## Conclusion

The TenderOwl.GirCoreTemplate.CSharp template significantly simplifies starting .NET application development for GNOME by handling all the complexity of initial build and deployment configuration. It allows you to focus on creating application functionality rather than tool configuration.

If you have questions or suggestions for improving the template, I'd be happy to discuss them in the [corresponding issue](https://github.com/gircore/gir.core/issues/938) of the Gir.Core repository.

Happy coding!
