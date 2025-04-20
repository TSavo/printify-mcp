# Printify MCP Server

A Model Context Protocol (MCP) server for integrating AI assistants with Printify's print-on-demand platform.

<a href="https://glama.ai/mcp/servers/@TSavo/printify-mcp">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/@TSavo/printify-mcp/badge" alt="Printify Server MCP server" />
</a>

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
  - [Starting the Server](#starting-the-server)
  - [Using with Claude Desktop](#using-with-claude-desktop)
  - [Using with Docker](#option-3-use-docker-recommended-for-isolation)
    - [Docker Hub Image](#option-3a-use-the-docker-image-directly-from-docker-hub)
    - [Docker Compose](#option-3b-build-and-run-with-docker-compose)
  - [Development Mode](#development-mode)
- [Available Tools](#available-tools)
  - [Shop Management](#shop-management)
  - [Product Management](#product-management)
  - [Blueprint and Variant Management](#blueprint-and-variant-management)
  - [Image Management](#image-management)
  - [Documentation](#documentation)
  - [Prompts](#prompts)
- [Workflow Examples](#workflow-examples)
  - [Creating a T-Shirt with AI-Generated Design](#creating-a-t-shirt-with-ai-generated-design)
  - [Managing Existing Products](#managing-existing-products)
- [Architecture](#architecture)
  - [Main Components](#main-components)
  - [Docker Architecture](#docker-architecture)
  - [Publishing the Docker Image](#publishing-the-docker-image)
  - [Using the Docker Image Without Node.js](#using-the-docker-image-without-nodejs)
  - [File Structure](#file-structure)
- [API Documentation](#api-documentation)
- [Troubleshooting](#troubleshooting)
  - [Common Issues](#common-issues)
  - [Docker-Specific Issues](#docker-specific-issues)
  - [Debugging](#debugging)
- [Contributing](#contributing)
- [License](#license)

## Overview

The Printify MCP Server is a bridge between AI assistants (like Claude) and Printify's print-on-demand platform. It allows AI assistants to create and manage print-on-demand products, generate designs using AI, and handle all aspects of product management through the Model Context Protocol (MCP).

MCP is an open standard developed by Anthropic that standardizes how applications provide context to Large Language Models (LLMs). This server implements the MCP specification to expose Printify's functionality to AI assistants in a structured way.

## Features

This MCP server provides the following capabilities:

### Printify API Integration

- **Authentication**: Initialize the Printify API client with your API key
- **Shops**: List and manage Printify shops
- **Products**: Create, read, update, delete, and publish products
- **Catalog**: Browse blueprints, print providers, and variants
- **Images**: Upload images to use in product designs

### AI Image Generation

- **Replicate Integration**: Generate images using Replicate's Flux 1.1 Pro model
- **Combined Workflow**: Generate images with AI and upload them directly to Printify in one step

### Documentation

- **In-Tool Documentation**: Comprehensive documentation for all aspects of product creation
- **Workflow Guidance**: Step-by-step guides for creating products

### Prompts

- **Generate Product Description**: Generate compelling product descriptions based on product details

## Prerequisites

- Node.js (v18 or higher)
- npm (v7 or higher)
- Printify API key
- Replicate API token (for AI image generation)
- ImgBB API key (required if using the Flux 1.1 Pro Ultra model)

## Installation

```bash
# Clone the repository
git clone https://github.com/tsavo/printify-mcp.git
cd printify-mcp

# Install dependencies
npm install

# Build the project
npm run build
```

## Configuration

You have two options for configuring the environment variables needed by the server:

### Option 1: Using a .env File (Recommended)

1. Create a `.env` file in the root directory of the project with the following variables:

```
# Required for all functionality
PRINTIFY_API_KEY=your_printify_api_key

# Required if using the Flux 1.1 Pro Ultra model for image generation
# The Ultra model generates high-resolution images that are too large for direct base64 upload
IMGBB_API_KEY=your_imgbb_api_key

# Optional: If not provided, the first shop in your account will be used
PRINTIFY_SHOP_ID=your_shop_id

# Optional: Only needed if you want to use image generation features
REPLICATE_API_TOKEN=your_replicate_api_token
```

You can use the `.env.example` file as a template by copying it:

```bash
cp .env.example .env
# Then edit the .env file with your actual API keys
```

### Option 2: Using System Environment Variables

Alternatively, you can set these variables directly in your system environment:

**Windows (Command Prompt):**
```cmd
:: Required
set PRINTIFY_API_KEY=your_printify_api_key

:: Optional
set PRINTIFY_SHOP_ID=your_shop_id

:: Optional - only for image generation
set REPLICATE_API_TOKEN=your_replicate_api_token
```

**Windows (PowerShell):**
```powershell
# Required
$env:PRINTIFY_API_KEY = "your_printify_api_key"

# Optional
$env:PRINTIFY_SHOP_ID = "your_shop_id"

# Optional - only for image generation
$env:REPLICATE_API_TOKEN = "your_replicate_api_token"
```

**macOS/Linux:**
```bash
# Required
export PRINTIFY_API_KEY=your_printify_api_key

# Optional
export PRINTIFY_SHOP_ID=your_shop_id

# Optional - only for image generation
export REPLICATE_API_TOKEN=your_replicate_api_token
```

The server will check for these environment variables at startup, regardless of whether they're set in a `.env` file or in the system environment.

### Getting a Printify API Key

1. Log in to your Printify account
2. Go to Settings > API
3. Click "Create New API Key"
4. Copy the API key and add it to your `.env` file

### Getting a Replicate API Token

1. Create an account on [Replicate](https://replicate.com/)
2. Go to your account settings
3. Generate an API token
4. Copy the token and add it to your `.env` file

## Usage

### Starting the Server

```bash
npm start
```

This will start the MCP server using the stdio transport, which allows it to communicate with MCP clients like Claude Desktop. The server will automatically initialize the Printify API client using the API key from the environment variable.

### Development Mode

```bash
npm run dev
```

This will start the server in development mode with automatic reloading when files change.

## Using with Claude Desktop

There are three ways to use this MCP server with Claude Desktop:

### Option 1: Install from npm (Recommended)

1. Install the package globally:
   ```bash
   npm install -g @tsavo/printify-mcp
   ```

2. Configure your environment variables using either a `.env` file in your current directory or system environment variables as described in the [Configuration](#configuration) section.

3. Configure Claude Desktop:
   - Open Claude Desktop
   - Go to Settings > MCP Servers
   - Click "Add Server"
   - Enter a name for the server (e.g., "Printify MCP")
   - Select "Command" as the transport type
   - Enter `printify-mcp` as the command
   - No arguments are needed
   - Click "Add Server"

4. Test the connection by asking Claude to check the Printify status:
   ```
   Can you check the status of my Printify connection?
   ```

   The `printify-mcp` command runs the same code as the original index.ts file, but packaged as an executable that can be run directly from the command line.

### Option 2: Use with npx

If you don't want to install the package globally, you can use npx:

1. Configure your environment variables as described in the [Configuration](#configuration) section.

2. Configure Claude Desktop:
   - Open Claude Desktop
   - Go to Settings > MCP Servers
   - Click "Add Server"
   - Enter a name for the server (e.g., "Printify MCP")
   - Select "Command" as the transport type
   - Enter `npx` as the command
   - Enter `@tsavo/printify-mcp` as the arguments
   - Click "Add Server"

### Option 3: Use Docker (Recommended for Isolation)

If you prefer to run the server in a Docker container, you have two options:

#### Option 3A: Use the Docker Image Directly from Docker Hub

1. Make sure you have Docker installed on your system

2. Create a directory for your Printify MCP files:
   ```bash
   mkdir printify-mcp
   cd printify-mcp
   ```

3. Create a `.env` file with your API keys:
   ```
   PRINTIFY_API_KEY=your_printify_api_key
   PRINTIFY_SHOP_ID=your_shop_id (optional)
   REPLICATE_API_TOKEN=your_replicate_api_token
   IMGBB_API_KEY=your_imgbb_api_key (required for Flux 1.1 Pro Ultra model)
   ```

4. Create a temp directory for temporary files:
   ```bash
   mkdir temp
   ```

5. Run the Docker container (two options):

   **Option A: Using environment variables directly (Recommended)**
   ```bash
   # For Linux/macOS/Windows PowerShell:
   docker run -it --name printify-mcp \
     -e PRINTIFY_API_KEY=your_printify_api_key \
     -e PRINTIFY_SHOP_ID=your_shop_id_optional \
     -v $(pwd)/temp:/app/temp \
     tsavo/printify-mcp:latest

   # For Windows Command Prompt:
   docker run -it --name printify-mcp ^
     -e PRINTIFY_API_KEY=your_printify_api_key ^
     -e PRINTIFY_SHOP_ID=your_shop_id_optional ^
     -v %cd%/temp:/app/temp ^
     tsavo/printify-mcp:latest
   ```

   **Note:** If you want to use the image generation features (generate-and-upload-image tool), add the Replicate API token:
   ```bash
   -e REPLICATE_API_TOKEN=your_replicate_api_token \
   ```

   **Important:** If you want to use the Flux 1.1 Pro Ultra model for image generation, you MUST also add the ImgBB API key:
   ```bash
   -e IMGBB_API_KEY=your_imgbb_api_key \
   ```

   **Option B: Using a .env file**
   ```bash
   # For Linux/macOS:
   docker run -it --name printify-mcp \
     -v $(pwd)/.env:/app/.env:ro \
     -v $(pwd)/temp:/app/temp \
     tsavo/printify-mcp:latest

   # For Windows PowerShell:
   docker run -it --name printify-mcp -v ${PWD}/.env:/app/.env:ro -v ${PWD}/temp:/app/temp tsavo/printify-mcp:latest

   # For Windows Command Prompt:
   docker run -it --name printify-mcp -v %cd%/.env:/app/.env:ro -v %cd%/temp:/app/temp tsavo/printify-mcp:latest
   ```

6. Configure Claude Desktop:
   - Open Claude Desktop
   - Go to Settings > MCP Servers
   - Click "Add Server"
   - Enter a name for the server (e.g., "Printify MCP Docker")
   - Select "Command" as the transport type
   - Enter `docker` as the command
   - Enter `exec -i printify-mcp node dist/index.js` as the arguments
   - Click "Add Server"

#### Option 3B: Build and Run with Docker Compose

1. Make sure you have Docker and Docker Compose installed on your system

2. Clone this repository to your local machine:
   ```bash
   git clone https://github.com/tsavo/printify-mcp.git
   cd printify-mcp
   ```

3. Configure environment variables (two options):

   **Option A: Edit docker-compose.yml directly (Recommended)**
   Open docker-compose.yml and uncomment/edit the environment variables:
   ```yaml
   environment:
     - NODE_ENV=production
     # Option 1: Set environment variables directly (recommended)
     - PRINTIFY_API_KEY=your_printify_api_key
     - PRINTIFY_SHOP_ID=your_shop_id_optional
     # Optional: Only needed if you want to use image generation features
     - REPLICATE_API_TOKEN=your_replicate_api_token
     # Required if using the Flux 1.1 Pro Ultra model for image generation
     - IMGBB_API_KEY=your_imgbb_api_key
   ```

   **Option B: Create a `.env` file**
   ```
   PRINTIFY_API_KEY=your_printify_api_key
   PRINTIFY_SHOP_ID=your_shop_id (optional)
   # Optional: Only needed if you want to use image generation features
   REPLICATE_API_TOKEN=your_replicate_api_token
   # Required if using the Flux 1.1 Pro Ultra model for image generation
   IMGBB_API_KEY=your_imgbb_api_key
   ```
   Then uncomment the .env volume mount in docker-compose.yml:
   ```yaml
   volumes:
     # Option 2: Mount a .env file for environment variables
     - ./.env:/app/.env:ro
   ```

4. Build and start the Docker container:
   ```bash
   docker-compose up -d
   ```

5. Configure Claude Desktop:
   - Open Claude Desktop
   - Go to Settings > MCP Servers
   - Click "Add Server"
   - Enter a name for the server (e.g., "Printify MCP Docker")
   - Select "Command" as the transport type
   - Enter `docker` as the command
   - Enter `exec -i printify-mcp node dist/index.js` as the arguments
   - Click "Add Server"

6. Test the connection by asking Claude to check the Printify status:
   ```
   Can you check the status of my Printify connection?
   ```

### Option 4: Clone and Set Up the Repository

If you prefer to work with the source code directly without Docker:

1. Clone this repository to your local machine:
   ```bash
   git clone https://github.com/tsavo/printify-mcp.git
   cd printify-mcp
   ```

2. Install dependencies and build the project:
   ```bash
   npm install
   npm run build
   ```

3. Configure your environment variables using either a `.env` file or system environment variables as described in the [Configuration](#configuration) section.

4. Get the full absolute path to the compiled JavaScript file:

   **Windows:**
   ```cmd
   cd dist
   echo %CD%\index.js
   ```

   **macOS/Linux:**
   ```bash
   realpath dist/index.js
   ```

5. Configure Claude Desktop:
   - Open Claude Desktop
   - Go to Settings > MCP Servers
   - Click "Add Server"
   - Enter a name for the server (e.g., "Printify MCP")
   - Select "Command" as the transport type
   - Enter the path to Node.js as the command (e.g., `node`)
   - Enter the **full absolute path** to the built server as the arguments
   - Click "Add Server"

6. Start the server:
   ```bash
   npm start
   ```

   Keep this terminal window open while you're using Claude Desktop.

### Testing the Connection

In a conversation with Claude, you can test if the server is working by asking Claude to check the Printify status:

```
Can you check the status of my Printify connection?
```

Claude should use the `get-printify-status` tool to check the connection status. You can also ask Claude to list your Printify shops using the `list-shops` tool.

If you encounter any issues:

1. Check the console output where you started the server for error messages
2. Verify that your environment variables are set correctly
3. Make sure the server is still running
4. Confirm that the path to the server in Claude Desktop is correct

## Available Tools

### Shop Management

#### `get-printify-status`

Get the current status of the Printify API client, including connection status and current shop.

#### `list-shops`

List all available shops in your Printify account. The currently selected shop is marked with an arrow (→).

#### `switch-shop`

Switch to a different shop for subsequent API calls.

Parameters:
- `shopId` (string): The ID of the shop to switch to

### Product Tools

#### `list-products`

List products in your Printify shop.

Parameters:
- `page` (number, optional): Page number (default: 1)
- `limit` (number, optional): Number of products per page (default: 10)

#### `get-product`

Get details of a specific product.

Parameters:
- `productId` (string): Product ID

#### `create-product`

Create a new product in your Printify shop.

Parameters:
- `title` (string): Product title
- `description` (string): Product description
- `blueprintId` (number): Blueprint ID
- `printProviderId` (number): Print provider ID
- `variants` (array): Product variants
- `printAreas` (object, optional): Print areas for the product

#### `update-product`

Update an existing product in your Printify shop.

Parameters:
- `productId` (string): Product ID
- `title` (string, optional): Product title
- `description` (string, optional): Product description
- `variants` (array, optional): Product variants
- `printAreas` (object, optional): Print areas for the product

#### `delete-product`

Delete a product from your Printify shop.

Parameters:
- `productId` (string): Product ID

#### `publish-product`

Publish a product to your connected sales channel.

Parameters:
- `productId` (string): Product ID
- `publishDetails` (object, optional): Publish details

### Catalog Tools

#### `get-blueprints`

Get a list of available blueprints from the Printify catalog.

Parameters:
- `page` (number, optional): Page number (default: 1)
- `limit` (number, optional): Number of blueprints per page (default: 10)

#### `get-blueprint`

Get details of a specific blueprint.

Parameters:
- `blueprintId` (string): Blueprint ID

#### `get-print-providers`

Get a list of print providers for a specific blueprint.

Parameters:
- `blueprintId` (string): Blueprint ID

#### `get-variants`

Get a list of variants for a specific blueprint and print provider.

Parameters:
- `blueprintId` (string): Blueprint ID
- `printProviderId` (string): Print provider ID

### Image Tools

#### `generate-and-upload-image`

Generate an image using Replicate's Flux models, process it with Sharp, and upload it to Printify in one operation. This tool combines AI image generation with Printify integration for a seamless workflow.

The tool performs four steps:
1. Generates an image using Replicate's Flux models based on your text prompt
2. Processes the image with Sharp to ensure it's a valid image with the correct format for Printify
3. Uploads the processed image to your Printify account
4. Cleans up temporary files to avoid disk space issues

Parameters:
- `prompt` (string): Text prompt for image generation
- `fileName` (string): File name for the uploaded image
- `model` (string, optional): Override the default model (e.g., "black-forest-labs/flux-1.1-pro-ultra")
- `width` (number, optional): Image width in pixels (default: 1024)
- `height` (number, optional): Image height in pixels (default: 1024)
- `aspectRatio` (string, optional): Aspect ratio (e.g., '16:9', '4:3', '1:1'). If provided, overrides width and height
- `outputFormat` (string, optional): Output format ("jpeg", "png", "webp") (default: "png")
- `numInferenceSteps` (number, optional): Number of inference steps (default: 25)
- `guidanceScale` (number, optional): Guidance scale (default: 7.5)
- `negativePrompt` (string, optional): Negative prompt (default: "low quality, bad quality, sketches")
- `seed` (number, optional): Random seed for reproducible generation
- `raw` (boolean, optional): Generate less processed, more natural-looking images (default: true for Flux 1.1 Pro Ultra)

**Note:** This tool requires the `REPLICATE_API_TOKEN` environment variable to be set with a valid Replicate API token. You can get a token from [replicate.com](https://replicate.com).

**Important:** If you want to use the Flux 1.1 Pro Ultra model, you MUST also set the `IMGBB_API_KEY` environment variable. The Ultra model generates high-resolution images that are too large for direct base64 upload to Printify. You can get a free API key from [api.imgbb.com](https://api.imgbb.com).

#### `generate-image`

Generate an image using Replicate's Flux models and save it to a local file without uploading to Printify. This tool is useful when you want to generate images for other purposes or when you want to review and potentially edit images before uploading them to Printify.

Parameters:
- `prompt` (string): Text prompt for image generation
- `outputPath` (string): Full path where the generated image should be saved
- `model` (string, optional): Override the default model (e.g., "black-forest-labs/flux-1.1-pro-ultra")
- `width` (number, optional): Image width in pixels (default: 1024)
- `height` (number, optional): Image height in pixels (default: 1024)
- `aspectRatio` (string, optional): Aspect ratio (e.g., '16:9', '4:3', '1:1'). If provided, overrides width and height
- `outputFormat` (string, optional): Output format ("jpeg", "png", "webp") (default: "png")
- `numInferenceSteps` (number, optional): Number of inference steps (default: 25)
- `guidanceScale` (number, optional): Guidance scale (default: 7.5)
- `negativePrompt` (string, optional): Negative prompt (default: "low quality, bad quality, sketches")
- `seed` (number, optional): Random seed for reproducible generation
- `raw` (boolean, optional): Generate less processed, more natural-looking images (Flux 1.1 Pro Ultra only)

**Note:** This tool requires the `REPLICATE_API_TOKEN` environment variable to be set with a valid Replicate API token. You can get a token from [replicate.com](https://replicate.com).

Unlike the `generate-and-upload-image` tool, this tool doesn't require the ImgBB API key since it saves directly to a local file.

#### `upload-image`

Upload an image to your Printify account. Supports three types of inputs:
1. URLs (http:// or https://) - Direct upload to Printify
2. Local file paths (e.g., c:\path\to\image.png) - Automatically converted using Sharp to ensure compatibility, then uploaded to Printify
3. Base64 encoded image strings - Direct upload to Printify

**Note on file formats:**
- Supported formats: PNG, JPEG, and SVG
- Recommended resolution for JPEG/PNG files is 300 DPI
- For larger products (leggings, blankets, tapestries), 120-150 DPI is acceptable
- Some image files may not be compatible with Printify's API if they exceed size limits
- For files larger than 5MB, URL upload is recommended over base64 encoding

Parameters:
- `fileName` (string): File name
- `url` (string): URL of the image to upload, path to local file, or base64 encoded image data

### Prompts

#### `generate-product-description`

Generate a compelling product description.

Parameters:
- `productName` (string): Name of the product
- `category` (string): Product category
- `targetAudience` (string, optional): Target audience for the product
- `keyFeatures` (string, optional): Comma-separated list of key product features

## Setting Up API Keys

### Printify API Key

To use the Printify features of this MCP server, you'll need a Printify API key. Here's how to get one and set it up:

1. Log in to your Printify account at [printify.com](https://printify.com/app/login)
2. Go to My Profile > Connections
3. In the Connections section, you can generate your Personal Access Tokens
4. Store your API key securely, as it will only be visible immediately after generation
5. Create a `.env` file in the project root with the following content:

   ```
   PRINTIFY_API_KEY=your_api_key_here
   # Optional: Set a default shop ID
   # PRINTIFY_SHOP_ID=your_shop_id_here

   # For image generation with Replicate
   REPLICATE_API_TOKEN=your_replicate_token_here

   # Required if using the Flux 1.1 Pro Ultra model for image generation
   IMGBB_API_KEY=your_imgbb_api_key_here
   ```

   The server will automatically initialize the Printify API client using the API key from the environment variable. If you don't specify a shop ID, the server will use the first shop in your account as the default.

   You can also set the environment variables directly:

   ```bash
   # On Windows
   set PRINTIFY_API_KEY=your_api_key_here
   set REPLICATE_API_TOKEN=your_replicate_token_here
   set IMGBB_API_KEY=your_imgbb_api_key_here
   npm start

   # On macOS/Linux
   export PRINTIFY_API_KEY=your_api_key_here
   export REPLICATE_API_TOKEN=your_replicate_token_here
   export IMGBB_API_KEY=your_imgbb_api_key_here
   npm start
   ```

### Replicate API Token

To use the image generation features of this MCP server, you'll need a Replicate API token. Here's how to get one:

1. Create an account or log in at [replicate.com](https://replicate.com)
2. Go to your account settings
3. Generate an API token
4. Add the token to your `.env` file as shown above

### ImgBB API Key

If you want to use the Flux 1.1 Pro Ultra model for image generation, you MUST have an ImgBB API key. The Ultra model generates high-resolution images that are too large for direct base64 upload to Printify, so we use ImgBB as an intermediary. Here's how to get an API key:

1. Create an account or log in at [imgbb.com](https://imgbb.com)
2. Go to [api.imgbb.com](https://api.imgbb.com) to get your API key
3. Add the key to your `.env` file as shown above

## Workflow Examples

### Creating a T-Shirt with AI-Generated Design

Here's a complete example of creating a t-shirt with front and back designs:

```javascript
// Step 1: Get blueprints and choose one
get-blueprints_printify()
// Selected blueprint ID 12 (Unisex Jersey Short Sleeve Tee)

// Step 2: Get print providers for this blueprint
get-print-providers_printify({ blueprintId: "12" })
// Selected print provider ID 29 (Monster Digital)

// Step 3: Get variants for this blueprint and print provider
get-variants_printify({ blueprintId: "12", printProviderId: "29" })
// Selected variant IDs 18100 (Black / S), 18101 (Black / M), 18102 (Black / L)

// Step 4: Generate and upload front image
const frontImage = await generate-and-upload-image_printify({
  prompt: "A futuristic cityscape with neon lights and tall skyscrapers, horizon city logo design",
  fileName: "horizon-city-front"
})
// Got image ID: 68032b22ae74bf725ed406ec

// Step 4b: Generate and upload back image
const backImage = await generate-and-upload-image_printify({
  prompt: "A minimalist 'Horizon City' text logo with futuristic font, suitable for the back of a t-shirt",
  fileName: "horizon-city-back"
})
// Got image ID: 68032b377e36fbdd32791027

// Step 5: Create the product
create-product_printify({
  title: "Horizon City Skyline T-Shirt",
  description: "Step into the future with our Horizon City Skyline T-Shirt. This premium unisex tee features a stunning futuristic cityscape with neon lights and towering skyscrapers on the front, and a sleek minimalist Horizon City logo on the back.",
  blueprintId: 12,
  printProviderId: 29,
  variants: [
    { variantId: 18100, price: 2499 },
    { variantId: 18101, price: 2499 },
    { variantId: 18102, price: 2499 }
  ],
  printAreas: {
    "front": { position: "front", imageId: "68032b22ae74bf725ed406ec" },
    "back": { position: "back", imageId: "68032b377e36fbdd32791027" }
  }
})
// Product created with ID: 68032b43a24efbac6502b6f7
```

### Managing Existing Products

```javascript
// List products
list-products_printify()

// Get details of a specific product
get-product_printify({ productId: "68032b43a24efbac6502b6f7" })

// Update a product
update-product_printify({
  productId: "68032b43a24efbac6502b6f7",
  title: "Updated Horizon City Skyline T-Shirt",
  description: "Updated description...",
  variants: [
    { variantId: 18100, price: 2999 },
    { variantId: 18101, price: 2999 },
    { variantId: 18102, price: 2999 }
  ]
})

// Publish a product to external sales channels
publish-product_printify({
  productId: "68032b43a24efbac6502b6f7",
  publishDetails: {
    title: true,
    description: true,
    images: true,
    variants: true,
    tags: true
  }
})

// Delete a product
delete-product_printify({ productId: "68032b43a24efbac6502b6f7" })
```

## Architecture

### Main Components

The Printify MCP server consists of three main components:

1. **MCP Server (`src/index.ts`)**: Sets up the MCP server with various tools for interacting with Printify's API.
2. **Printify API Client (`src/printify-api.ts`)**: Handles communication with Printify's API using the official SDK.
3. **Replicate Client (`src/replicate-client.ts`)**: Integrates with Replicate's API to generate images for product designs.

### Docker Architecture

The Docker setup consists of the following components:

1. **Dockerfile**: Defines how to build the Docker image
   - Uses Node.js 22 Alpine as the base image for a small footprint
   - Installs dependencies and builds the TypeScript code
   - Sets up the environment and runs the server

2. **docker-compose.yml**: Defines the service configuration
   - Sets up environment variables
   - Mounts volumes for .env file and temp directory
   - Configures stdin and tty for stdio transport
   - Sets restart policy

3. **Volumes**:
   - `.env`: Mounted as a read-only volume for environment variables
   - `temp`: Mounted as a volume for temporary files (like generated images)

### Publishing the Docker Image

You can publish the Docker image to Docker Hub or any other container registry to make it available to others without requiring them to install Node.js or clone the repository.

1. **Build the Docker image**:
   ```bash
   docker build -t tsavo/printify-mcp:latest .
   ```

2. **Log in to Docker Hub**:
   ```bash
   docker login
   ```

3. **Push the image to Docker Hub**:
   ```bash
   docker push tsavo/printify-mcp:latest
   ```

### Using the Docker Image Without Node.js

Users can run the Printify MCP server without installing Node.js by using the Docker image directly:

1. **Install Docker**: Users need to have Docker installed on their system

2. **Create a temp directory** for temporary files:
   ```bash
   mkdir -p temp
   ```

3. **Run the Docker container** (two options):

   **Option A: Using environment variables directly (Recommended)**
   ```bash
   docker run -it --name printify-mcp \
     -e PRINTIFY_API_KEY=their_printify_api_key \
     -e PRINTIFY_SHOP_ID=their_shop_id_optional \
     -v $(pwd)/temp:/app/temp \
     tsavo/printify-mcp:latest
   ```

   **Note:** If they want to use the image generation features (generate-and-upload-image tool), add the Replicate API token:
   ```bash
   -e REPLICATE_API_TOKEN=their_replicate_api_token \
   ```

   **Important:** If they want to use the Flux 1.1 Pro Ultra model for image generation, they MUST also add the ImgBB API key:
   ```bash
   -e IMGBB_API_KEY=their_imgbb_api_key \
   ```

   **Option B: Using a .env file**
   First, create a .env file with their API keys:
   ```
   PRINTIFY_API_KEY=their_printify_api_key
   PRINTIFY_SHOP_ID=their_shop_id (optional)
   # Optional: Only needed if they want to use image generation features
   REPLICATE_API_TOKEN=their_replicate_api_token
   # Required if using the Flux 1.1 Pro Ultra model for image generation
   IMGBB_API_KEY=their_imgbb_api_key
   ```

   Then run the container:
   ```bash
   docker run -it --name printify-mcp \
     -v $(pwd)/.env:/app/.env:ro \
     -v $(pwd)/temp:/app/temp \
     tsavo/printify-mcp:latest
   ```

4. **Configure Claude Desktop**:
   - Open Claude Desktop
   - Go to Settings > MCP Servers
   - Click "Add Server"
   - Enter a name for the server (e.g., "Printify MCP Docker")
   - Select "Command" as the transport type
   - Enter `docker` as the command
   - Enter `exec -i printify-mcp node dist/index.js` as the arguments
   - Click "Add Server"

This approach allows users to run the Printify MCP server without installing Node.js or any other dependencies - they only need Docker.

### File Structure

```
printify-mcp/
├── dist/                  # Compiled JavaScript files
├── docs/                  # Documentation
│   ├── index.ts.md        # Documentation for index.ts
│   ├── printify-api.ts.md # Documentation for printify-api.ts
│   └── replicate-client.ts.md # Documentation for replicate-client.ts
├── node_modules/          # Node.js dependencies
├── src/                   # Source code
│   ├── index.ts           # Main MCP server
│   ├── printify-api.ts    # Printify API client
│   └── replicate-client.ts # Replicate API client
├── temp/                  # Temporary directory for generated images
├── .dockerignore          # Files to exclude from Docker build
├── .env                   # Environment variables (not in repo)
├── .env.example           # Example environment variables
├── .gitignore             # Git ignore file
├── docker-compose.yml     # Docker Compose configuration
├── Dockerfile             # Docker build instructions
├── package.json           # Node.js package configuration
├── package-lock.json      # Node.js package lock
├── README.md              # This file
└── tsconfig.json          # TypeScript configuration
```

## API Documentation

For detailed documentation of the codebase, see the following files:

- [index.ts Documentation](docs/index.ts.md)
- [printify-api.ts Documentation](docs/printify-api.ts.md)
- [replicate-client.ts Documentation](docs/replicate-client.ts.md)

## Troubleshooting

### Common Issues

#### Printify API Client Not Initialized

If you see the error "Printify API client is not initialized", check that:

1. The `PRINTIFY_API_KEY` environment variable is set correctly in your `.env` file
2. The API key is valid and has the correct permissions

#### Replicate API Client Not Initialized

If you see the error "Replicate API client is not initialized", check that:

1. The `REPLICATE_API_TOKEN` environment variable is set correctly in your `.env` file
2. The API token is valid and has the correct permissions

#### Error Creating Product

If you encounter errors when creating a product, check that:

1. The blueprint ID and print provider ID are valid
2. The variant IDs are valid for the selected blueprint and print provider
3. The image IDs in print areas are valid and accessible
4. All required fields are included in the request

#### Error Uploading Image

If you encounter errors when uploading an image, check that:

1. The image is a valid format (PNG, JPEG, etc.)
2. The image is not too large (maximum size is 10MB)
3. If using a URL, it is publicly accessible
4. If using a local file, it exists and is readable

#### Docker-Specific Issues

If you're using the Docker setup and encounter issues:

1. **Container not starting**: Check Docker logs with `docker logs printify-mcp`
2. **Environment variables not working**: If using a .env file, make sure it's in the same directory as your docker-compose.yml file or the directory where you run the `docker run` command. If setting environment variables directly with `-e`, check for typos in variable names
3. **Permission issues with temp directory**: The temp directory is mounted as a volume, ensure it has the correct permissions
4. **Connection issues from Claude**: Make sure the Docker container is running with `docker ps` and that you've configured Claude Desktop correctly
5. **Image not found**: If using the Docker Hub image directly, make sure you've pulled it with `docker pull tsavo/printify-mcp:latest`

To restart the Docker container when using docker-compose:
```bash
docker-compose down
docker-compose up -d
```

To restart the Docker container when using docker run:
```bash
docker stop printify-mcp
docker rm printify-mcp
docker run -it --name printify-mcp -v $(pwd)/.env:/app/.env:ro -v $(pwd)/temp:/app/temp tsavo/printify-mcp:latest
```

For Windows users using PowerShell with the Docker image directly:
```powershell
docker run -it --name printify-mcp -v ${PWD}/.env:/app/.env:ro -v ${PWD}/temp:/app/temp tsavo/printify-mcp:latest
```

For Windows users using Command Prompt with the Docker image directly:
```cmd
docker run -it --name printify-mcp -v %cd%/.env:/app/.env:ro -v %cd%/temp:/app/temp tsavo/printify-mcp:latest
```

### Debugging

The server includes detailed logging to help troubleshoot issues. Check the console output for error messages and debugging information.

For Docker deployments, you can view logs with:
```bash
docker logs printify-mcp
```

To follow the logs in real-time:
```bash
docker logs -f printify-mcp
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

ISC