# MCP
Example 1: File System MCP Server¶
This example demonstrates connecting to a local MCP server that provides file system operations.

Step 1: Define your Agent with MCPToolset¶
Create an agent.py file (e.g., in ./adk_agent_samples/mcp_agent/agent.py). The MCPToolset is instantiated directly within the tools list of your LlmAgent.

Important: Replace "/path/to/your/folder" in the args list with the absolute path to an actual folder on your local system that the MCP server can access.
Important: Place the .env file in the parent directory of the ./adk_agent_samples directory.

# ./adk_agent_samples/mcp_agent/agent.py
import os # Required for path operations
from google.adk.agents import LlmAgent
from google.adk.tools.mcp_tool.mcp_toolset import MCPToolset, StdioServerParameters

# It's good practice to define paths dynamically if possible,
# or ensure the user understands the need for an ABSOLUTE path.
# For this example, we'll construct a path relative to this file,
# assuming '/path/to/your/folder' is in the same directory as agent.py.
# REPLACE THIS with an actual absolute path if needed for your setup.
TARGET_FOLDER_PATH = os.path.join(os.path.dirname(os.path.abspath(__file__)), "/path/to/your/folder")
# Ensure TARGET_FOLDER_PATH is an absolute path for the MCP server.
# If you created ./adk_agent_samples/mcp_agent/your_folder,

root_agent = LlmAgent(
    model='gemini-2.0-flash',
    name='filesystem_assistant_agent',
    instruction='Help the user manage their files. You can list files, read files, etc.',
    tools=[
        MCPToolset(
            connection_params=StdioServerParameters(
                command='npx',
                args=[
                    "-y",  # Argument for npx to auto-confirm install
                    "@modelcontextprotocol/server-filesystem",
                    # IMPORTANT: This MUST be an ABSOLUTE path to a folder the
                    # npx process can access.
                    # Replace with a valid absolute path on your system.
                    # For example: "/Users/youruser/accessible_mcp_files"
                    # or use a dynamically constructed absolute path:
                    os.path.abspath(TARGET_FOLDER_PATH),
                ],
            ),
            # Optional: Filter which tools from the MCP server are exposed
            # tool_filter=['list_directory', 'read_file']
        )
    ],
)
Step 2: Create an __init__.py file¶
Ensure you have an __init__.py in the same directory as agent.py to make it a discoverable Python package for ADK.


# ./adk_agent_samples/mcp_agent/__init__.py
from . import agent
Step 3: Run adk web and Interact¶
Navigate to the parent directory of mcp_agent (e.g., adk_agent_samples) in your terminal and run:


cd ./adk_agent_samples # Or your equivalent parent directory
adk web
Note for Windows users

When hitting the _make_subprocess_transport NotImplementedError, consider using adk web --no-reload instead.

Once the ADK Web UI loads in your browser:

Select the filesystem_assistant_agent from the agent dropdown.
Try prompts like:
"List files in the current directory."
"Can you read the file named sample.txt?" (assuming you created it in TARGET_FOLDER_PATH).
"What is the content of another_file.md?"
You should see the agent interacting with the MCP file system server, and the server's responses (file listings, file content) relayed through the agent. The adk web console (terminal where you ran the command) might also show logs from the npx process if it outputs to stderr.

MCP with ADK Web - FileSystem Example

Example 2: Google Maps MCP Server¶
This example demonstrates connecting to the Google Maps MCP server.

Step 1: Get API Key and Enable APIs¶
Google Maps API Key: Follow the directions at Use API keys to obtain a Google Maps API Key.
Enable APIs: In your Google Cloud project, ensure the following APIs are enabled:
Directions API
Routes API For instructions, see the Getting started with Google Maps Platform documentation.
