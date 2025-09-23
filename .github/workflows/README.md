# Workflows in matlab-integration-for-jupyter

This repository uses workflows to build the Dockerfiles hosted in this repository and publish them to GHCR.

## Overview

There are 2 kinds of YML files used here:
1. `build-and-publish-docker-image.yml`, which specifies a reusable workflow, which MUST be called from a workflow configuration file.
2. Other YML files in the `.github/workflows` directory call this reusable-workflow.

## Triggers and Scheduled Jobs

All workflows are scheduled to run on Monday at 00:00.
Workflows are also triggered when you push any changes to the directories with Dockerfiles.
Workflows can be manually triggered from the "Actions" tab.

----

Copyright 2023-2025 The MathWorks, Inc.

----
