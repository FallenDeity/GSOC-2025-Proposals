"Extend type support for django-stubs" proposal for Google Summer of Code 2025

| Categories | Details                                                                      |
|------------|------------------------------------------------------------------------------|
| Difficulty | Hard                                                                         |
| Size       | 350hrs                                                                       |
| Key Skills | Python, Django, Type Hints, Static Type Checking, MyPy, Pyright/Pylance, Git |

- [1. Abstract](#1-abstract)
  - [1.1 Overview](#11-overview)
  - [1.2 Goals](#12-goals)
  - [1.3 Benefits](#13-benefits)
- [2. Approach](#2-approach)
  - [2.1 Identifying Missing Type Hints](#21-identifying-missing-type-hints)
  - [2.2 Adding Type Hints](#22-adding-type-hints)
  - [2.3 Testing](#23-testing)
- [3. Schedule and Milestones](#3-schedule-and-milestones)
  - [3.1 Community Bonding Period (May 8 - June 1)](#31-community-bonding-period-may-8---june-1)
  - [3.2 Phase 1 (June 2 - July 14)](#32-phase-1-june-2---july-14)
    - [3.2.1 Work on `BaseModelAdmin` and related entries (2-2.5 weeks)](#321-work-on-basemodeladmin-and-related-entries-2-25-weeks)
    - [3.2.2 Work on `django.db.backends.*` and related entries (3-3.5 weeks)](#322-work-on-djangodbbackends-and-related-entries-3-35-weeks)
  - [3.3 Phase 2 (July 15 - August 25)](#33-phase-2-july-15---august-25)
    - [3.3.1 Survey and identify entries related to `django.contrib.gis.db.models.*` (1-1.5 weeks)](#331-survey-and-identify-entries-related-to-djangocontribgisdbmodels-1-15-weeks)
    - [3.3.2 Work on `django.contrib.gis.db.models.*` and related entries (4-4.5 weeks)](#332-work-on-djangocontribgisdbmodels-and-related-entries-4-45-weeks)
    - [3.3.3 Final Testing and Documentation (1-1.5 weeks)](#333-final-testing-and-documentation-1-15-weeks)
- [4. About Me](#4-about-me)
  - [4.1 Contibutions](#41-contibutions)
  - [4.2 Contact](#42-contact)

# 1. Abstract

## 1.1 Overview

The Django project is a high-level Python web framework that encourages rapid development and clean, pragmatic design. It is maintained by a large community of developers and contributors. The Django project has a strong focus on code quality and maintainability, however it doesn't come with inline type hints. This is where django-stubs comes in. Django-stubs is a third party package that provides type hints for Django, allowing developers to take advantage of static type checkers like mypy, pyright/pylance etc to identify potential issues in their code before runtime and during development.

However, the current coverage of type hints in django-stubs is not exhaustive. There are many areas where type hints are missing or incomplete, which limits the effectiveness of static type checking in Django projects, forcing developers to add `# type: ignore` or similar workarounds native to the type checker they are using. As of the time of writing, django-stubs has approximately `~1600` missing type hints.

This proposal aims to extend the type support for django-stubs by adding type hints to various parts of the Django codebase. This will improve the overall quality of the code and make it easier for developers to use Django with static type checkers. Improving type hint coverage will also enhance the developer experience and reduce the likelihood of runtime errors.

## 1.2 Goals

`django-stubs` defines 3 allowlists in their `scripts/allowlists` directory. These allowlists are used to determine which items should be included in the type hinting process, and stuff which we can ignore. The allowlists are:

- `scripts/stubtest/allowlist.txt` where we store things that we really don't care about: hacks, django internal utility modules, things that are handled by our plugin, things that are not representable by type system, etc
- `scripts/stubtest/allowlist_todo.txt` where we store all errors there are right now. Basically, this is a TODO list: we need to work through this list and fix things (or move entries to real allowlist.txt). In the end, ideally we can remove this file.
- `scripts/stubtest/allowlist_todo_django51.txt` where we store new errors from the Django 5.0 to 5.1 upgrade. This is an extra TODO list.

The goal of this project is to work through the `allowlist_todo.txt` and `allowlist_todo_django51.txt` files and fix as many entries as possible. This will involve adding type hints to various parts of the Django codebase, as well as updating the allowlists to reflect the changes made, along with writing tests to ensure that the changes are correct and do not introduce any new issues.

While the exact number of entries that can be fixed is not known, I will aim to fix the following entries and majorly address them in this proposal when drawing up the timeline and schedule:

- Entries related to `BaseModelAdmin (contrib/admin/options.pyi)` and related entries inheriting from it, and their properties and methods, i.e most of the `ModelAdmin` related entries. (~30 entries)
- Entries related to `django.contrib.gis.db.models.*` where most of the focus would be on the `Field (comes from django.db.models)` base class and inheriting classes/interfaces. Which would cover a lot of field specific attributes and methods. (~100 entries `[lines 227-349 approx]`)
- Entries related to `django.db.backends.*`, which would cover the database backends/wrappers such as postgres, mysql, sqlite3, etc. (~50 entries)

## 1.3 Benefits

- Improved type hint coverage in django-stubs will lead to better static type checking and improved code quality in Django projects.
- Developers will be able to catch potential issues earlier in the development process, reducing the likelihood of runtime errors and improving overall productivity.
- Better ci/cd integration with type checkers will lead to more reliable and maintainable codebases.
- Facilitates better IDEs and editor support for Django projects, making it easier for developers to work with the framework.
- Reduces divergence between the Django codebase and django-stubs, making it easier for developers to use both together.

# 2. Approach

## 2.1 Identifying Missing Type Hints

The first step in this process will be to identify the missing type hints in the Django codebase. This will involve reviewing the `allowlist_todo.txt` and `allowlist_todo_django51.txt` files and identifying the entries that need to be addressed. I will also review the Django codebase to understand the context of each entry and determine the best way to add type hints. 

An example of an entry in the `allowlist_todo.txt` file is:

```
django.contrib.gis.admin.options.GISModelAdmin
```

This entry indicates that the `GISModelAdmin` class in the `django.contrib.gis.admin.options` module is missing type hints and a mixin class `GeoModelAdminMixin`. I will need to review the `GISModelAdmin` class and its methods to determine the appropriate type hints to add. This will involve understanding the purpose of each method and its parameters, as well as the expected return types. I will also need to consider any dependencies or interactions with other parts of the Django codebase. Additionally, I will document my findings and proposed changes on a per pr basis in the PR description, so that the changes can be reviewed and discussed with the Django Stubs maintainers, while making sure each PR is self-contained and focused on a specific area of the codebase. This will help to ensure that the changes are easy to review and understand, and will also make it easier to track progress on the project as a whole.

## 2.2 Adding Type Hints

Once the missing type hints have been identified, the next step will be to add them to the `django-stubs` codebase. This will involve modifying the relevant files in the `django-stubs` repository and adding type hints to the appropriate classes, methods, and parameters. I will also need to update any relevant tests to ensure that the changes are correct and do not introduce any new issues.

An example of a type hint that could be added is:

- Type Stub
  
```python
from typing import Any
from django.contrib.admin import ModelAdmin
from django.contrib.gis.forms.widgets import BaseGeometryWidget
from django.db.models.fields import Field
from django.http.request import HttpRequest


class GeoModelAdminMixin:
    gis_widget: type[BaseGeometryWidget]
    gis_widget_kwargs: dict[str, Any]

    def formfield_for_dbfield(
        self,
        db_field: Field[Any, Any],
        request: HttpRequest,
        **kwargs: Any,
    ) -> Any:
        ...

class GISModelAdmin(GeoModelAdminMixin, ModelAdmin[Any]):
    ...
```

- Source Defintion

```python
from django.contrib.admin import ModelAdmin
from django.contrib.gis.db import models
from django.contrib.gis.forms import OSMWidget


class GeoModelAdminMixin:
    gis_widget = OSMWidget
    gis_widget_kwargs = {}

    def formfield_for_dbfield(self, db_field, request, **kwargs):
        if isinstance(db_field, models.GeometryField) and (
            db_field.dim < 3 or self.gis_widget.supports_3d
        ):
            kwargs["widget"] = self.gis_widget(**self.gis_widget_kwargs)
            return db_field.formfield(**kwargs)
        else:
            return super().formfield_for_dbfield(db_field, request, **kwargs)


class GISModelAdmin(GeoModelAdminMixin, ModelAdmin):
    pass
```

We can finally check if the stubs and the source code are in sync by running the `scripts/stubtest.sh` script. This script will check the stubs against the source code and report any discrepancies. If there are any discrepancies, I will need to review the changes and make any necessary adjustments to ensure that the stubs are accurate and up-to-date.

```bash
$ ./scripts/stubtest.sh
note: unused allowlist entry django.contrib.gis.admin.GISModelAdmin.gis_widget
note: unused allowlist entry django.contrib.gis.admin.options.GISModelAdmin.gis_widget
note: unused allowlist entry django.contrib.gis.admin.options.GeoModelAdminMixin
```

We can remove the unused entries from the allowlist and run the script again to ensure that there are no discrepancies. This process will be repeated for each entry in the allowlist until all entries have been addressed.

## 2.3 Testing

Once the type hints have been added, I will need to test the changes to ensure that they are correct and do not introduce any new issues. This will involve running the existing tests in the `django-stubs` repository, as well as writing new tests to cover the changes made. I will also need to ensure that the changes do not break any existing functionality in the Django codebase. This will involve running the tests in the Django repository and ensuring that all tests pass.

> [!Note]
> This process is volatile and there might be unexpected challenges along the way, so flexibility and adaptability will be key to success. I will also need to communicate with the Django Stubs maintainers and the Django community to ensure that the changes are well-received and that any issues are addressed in a timely manner.
> 
> Special focus will be given to community feedback and suggestions, to ensure that the changes are aligned with the needs and expectations of the community. I will also be open to suggestions and feedback from the community, and will be willing to make adjustments to the proposal as needed.

# 3. Schedule and Milestones

I would be working on this project for a total of 350 hours over the course of 12 weeks. On average, I would be devoting 30 hours per week to this project, with roughly 2-3 hours on weekdays and 5-6 hours on weekends. 

## 3.1 Community Bonding Period (May 8 - June 1)

- Familiarize myself with the Django codebase and the django-stubs repositories and particular to the areas I will be working on.
- Communicate with the Django Stubs maintainers and the Django community to gather feedback and suggestions on approaches to take and areas to focus on.

## 3.2 Phase 1 (June 2 - July 14)

### 3.2.1 Work on `BaseModelAdmin` and related entries (2-2.5 weeks)

- Review the `BaseModelAdmin` class and its methods to determine the appropriate type hints to add.
- Add type hints to the `BaseModelAdmin` class and its methods, as well as any related classes or methods.

### 3.2.2 Work on `django.db.backends.*` and related entries (3-3.5 weeks)

- Analyze the source code and the allowlist entries related to `django.db.backends.*` and its database wrapper interfaces.
- Review the `django.db.backends.*` classes and their methods to determine the appropriate type hints to add.
- Add type hints to the `django.db.backends.*` classes and their methods, as well as any related classes or methods.

## 3.3 Phase 2 (July 15 - August 25)

### 3.3.1 Survey and identify entries related to `django.contrib.gis.db.models.*` (1-1.5 weeks)

- Survey the `allowlist_todo.txt` and `allowlist_todo_django51.txt` files to identify entries related to `django.contrib.gis.db.models.*`.
- Group the entries into categories based on their functionality and dependencies and analyze the source code to understand the context of each entry.
- Create a roadmap for addressing particular entries and their dependencies, and document the findings in a shared document for review and feedback from the Django Stubs maintainers and the Django community.
- Prioritize the entries based on their complexity and impact on the overall type hint coverage.

An example of an entry in the `allowlist_todo.txt` file is:

```
# This is dependent on the django.db.models.Field class, 
# methods such as __copy__, __deepcopy__, __lt__ etc.
django.contrib.gis.db.models.Field
...
```

### 3.3.2 Work on `django.contrib.gis.db.models.*` and related entries (4-4.5 weeks)

- Add type hints based on the roadmap created in the previous step. 
- Add any necessary tests to ensure that the changes are correct and in line with expected behavior.
- Create a PR for each entry or group of entries, and document the changes made in the PR description.

### 3.3.3 Final Testing and Documentation (1-1.5 weeks)

- Make sure any documentation needed is comprehensive, and all proposed changes are well-documented and tested.
- Provide a summary of the changes made and their impact on the overall type hint coverage in django-stubs along with usage examples and guidelines if needed.
- Wrap up any pending tasks or feedback from the Django Stubs maintainers and the Django community.
- Prepare for the final evaluation and submit the final report.

# 4. About Me

I am `Triyan Mukherjee` and currently a third year undergraduate student at the `Manipal Institute of Technology` in India, pursuing a degree in Computer and Communication Engineering. I have a strong background in Python and have worked with multiple web frameworks. I have experience with static type checking using mypy and pyright/pylance, and have developed my own libraries and packages in the past with complete type coverage and published them to PyPI. 

- [Pokelance](https://github.com/FallenDeity/PokeLance)

I have worked with Django in the past and have a good understanding of its architecture and design principles. I have also contributed to open source projects in the past, including cli tools such as `onefetch` and large scale open source API's using `django` such as `PokeAPI`.

## 4.1 Contibutions

Currently working on a PR for `django-stubs` to add type hints to the `django.db.models.Field` class and related interfaces which adds type hints to all inbuilt and user-defined fields in Django. This PR is currently a wip and I am working with the maintainers to address any feedback and suggestions.

- [#2590](https://github.com/typeddjango/django-stubs/pull/2590)
- Reference: [#2215](https://github.com/typeddjango/django-stubs/pull/2214)

## 4.2 Contact

- Email: [triyanmukherjee@gmail.com](mailto:triyanmukherjee@gmail.com)
- LinkedIn: [triyanmukherjee](https://www.linkedin.com/in/triyan-mukherjee/)
- GitHub: [FallenDeity](https://github.com/FallenDeity)
- Timezone: GMT+5:30 (IST)