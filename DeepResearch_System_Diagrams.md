# DeepResearch System Architecture Diagrams

This document contains detailed Mermaid diagrams illustrating the DeepResearch agentic system architecture, workflows, and component interactions.

## 1. Overall System Architecture

```mermaid
graph TB
    subgraph "DeepResearch Agentic System"
        subgraph "Core Models"
            DR[DeepResearch-30B-A3B<br/>Main Agent Model<br/>128K Context]
            AF[AgentFounder-30B<br/>Continual Pre-training<br/>Agentic CPT]
            WS[WebSailor Models<br/>3B/7B/32B/72B<br/>Super-human Reasoning]
            WW[WebWatcher Models<br/>7B/32B<br/>Vision-Language]
            WR[WebResearcher<br/>Iterative Deep Research<br/>Unbounded Reasoning]
            WD[WebDancer-32B<br/>Autonomous Search<br/>Native Agentic]
        end
        
        subgraph "Tool Ecosystem"
            SEARCH[Search Tool<br/>Google/Serper API<br/>Batched Queries]
            VISIT[Visit Tool<br/>Webpage Extraction<br/>Goal-oriented Summary]
            SCHOLAR[Scholar Tool<br/>Academic Search<br/>Publication Metadata]
            PYTHON[Python Interpreter<br/>Sandboxed Execution<br/>Multi-endpoint]
            FILE[File Parser<br/>Multi-format Support<br/>PDF/DOCX/CSV/etc]
            IMAGE[Image Search<br/>Visual Content<br/>OCR Processing]
        end
        
        subgraph "Inference Paradigms"
            REACT[ReAct Framework<br/>Reasoning + Acting<br/>Standard Evaluation]
            ITER[IterResearch<br/>Heavy Mode<br/>Max Performance]
            RESUM[ReSum Paradigm<br/>Context Summarization<br/>Memory Efficient]
        end
        
        subgraph "Memory & Context"
            CONTEXT[Context Management<br/>128K Token Limit<br/>Auto Truncation]
            MEMORY[Memory Systems<br/>Open-world Memory<br/>Continuous Streams]
            SUMMARY[Summary Reports<br/>Evolving Knowledge<br/>Central Memory]
        end
        
        subgraph "Training Pipeline"
            SFT[Supervised Fine-tuning<br/>RFT Cold Start<br/>Expert Trajectories]
            RL[Reinforcement Learning<br/>GRPO/DUPO/RLVR<br/>Policy Optimization]
            CPT[Continual Pre-training<br/>Agentic CPT<br/>32K/128K Contexts]
        end
    end
    
    DR --> REACT
    DR --> ITER
    AF --> CPT
    WS --> SFT
    WS --> RL
    WW --> IMAGE
    WR --> SUMMARY
    WD --> REACT
    
    REACT --> SEARCH
    REACT --> VISIT
    REACT --> SCHOLAR
    REACT --> PYTHON
    REACT --> FILE
    
    ITER --> RESUM
    RESUM --> CONTEXT
    CONTEXT --> MEMORY
    MEMORY --> SUMMARY
```

## 2. Agent Workflow Patterns

### ReAct Framework Workflow

```mermaid
graph TD
    START[User Query] --> PARSE[Parse Query & Initialize]
    PARSE --> INIT[Initialize Context<br/>System Prompt + Date]
    
    INIT --> THINK[Think<br/>Internal Reasoning]
    THINK --> DECIDE{Decision Point}
    
    DECIDE -->|Tool Needed| TOOL_CALL[Tool Call<br/>JSON Format]
    DECIDE -->|Answer Ready| FINAL_ANSWER[Final Answer<br/>Answer Tags]
    
    TOOL_CALL --> EXECUTE[Execute Tool]
    EXECUTE --> OBSERVE[Observe Results<br/>Tool Response]
    OBSERVE --> UPDATE[Update Context<br/>Add to Messages]
    
    UPDATE --> CHECK_LIMITS{Check Limits}
    CHECK_LIMITS -->|Token Limit| TRUNCATE[Truncate Context<br/>Force Answer]
    CHECK_LIMITS -->|Call Limit| EXHAUST[Exhaust Calls<br/>No Answer]
    CHECK_LIMITS -->|Time Limit| TIMEOUT[Timeout<br/>2.5 Hours]
    CHECK_LIMITS -->|Continue| THINK
    
    TRUNCATE --> FINAL_ANSWER
    EXHAUST --> FINAL_ANSWER
    TIMEOUT --> FINAL_ANSWER
    FINAL_ANSWER --> END[End Process]
```

### IterResearch Paradigm Workflow

```mermaid
graph TD
    START[User Query] --> INIT[Initialize Research]
    INIT --> ROUND[Start Research Round]
    
    ROUND --> WORKSPACE[Create Lean Workspace<br/>Reconstructed Context]
    WORKSPACE --> THINK[Think<br/>Internal Monologue<br/>Not Passed Forward]
    
    THINK --> REPORT[Report<br/>Synthesize Findings<br/>Update Summary]
    REPORT --> ACTION{Action Decision}
    
    ACTION -->|Tool Call| TOOL[Execute Tool]
    ACTION -->|Final Answer| ANSWER[Provide Answer]
    
    TOOL --> RESULT[Process Tool Result]
    RESULT --> UPDATE_MEMORY[Update Central Memory<br/>Evolving Summary]
    
    UPDATE_MEMORY --> CHECK_COMPLETE{Task Complete?}
    CHECK_COMPLETE -->|No| ROUND
    CHECK_COMPLETE -->|Yes| ANSWER
    
    ANSWER --> END[End Research]
```

### ReSum Paradigm Workflow

```mermaid
graph TD
    START[User Query] --> EXPLORE[Initial Exploration<br/>Standard ReAct]
    EXPLORE --> ACCUMULATE[Accumulate Context<br/>Tool Results]
    
    ACCUMULATE --> CHECK_CONTEXT{Context Size<br/>Check}
    CHECK_CONTEXT -->|Within Limit| CONTINUE[Continue Exploration]
    CHECK_CONTEXT -->|Exceeds Limit| SUMMARIZE[Summarize Context<br/>ReSumTool-30B]
    
    SUMMARIZE --> COMPRESS[Compress History<br/>Extract Key Evidence]
    COMPRESS --> RESTART[Restart with Summary<br/>Clean Context]
    
    RESTART --> EXPLORE
    CONTINUE --> CHECK_COMPLETE{Task Complete?}
    CHECK_COMPLETE -->|No| ACCUMULATE
    CHECK_COMPLETE -->|Yes| FINAL[Final Answer]
    
    FINAL --> END[End Process]
```

## 3. Tool Interaction Architecture

```mermaid
graph LR
    subgraph "Agent Layer"
        AGENT[DeepResearch Agent<br/>MultiTurnReactAgent]
    end
    
    subgraph "Tool Selection Layer"
        TOOL_MAP[Tool Map<br/>Name to Tool Mapping]
        TOOL_CLASS[Tool Classes<br/>FileParser, Scholar, Visit, Search, PythonInterpreter]
    end
    
    subgraph "Tool Execution Layer"
        SEARCH[Search Tool<br/>Serper API]
        VISIT[Visit Tool<br/>Jina API]
        SCHOLAR[Scholar Tool<br/>Google Scholar API]
        PYTHON[Python Tool<br/>Sandbox Fusion]
        FILE[File Parser<br/>Multi-format Support]
    end
    
    subgraph "External Services"
        SERPER[Serper API<br/>Google Search]
        JINA[Jina API<br/>Web Content]
        SCHOLAR_API[Google Scholar<br/>Academic Search]
        SANDBOX[Sandbox Fusion<br/>Code Execution]
        IDP[IDP Service<br/>Document Parsing]
    end
    
    AGENT --> TOOL_MAP
    TOOL_MAP --> TOOL_CLASS
    TOOL_CLASS --> SEARCH
    TOOL_CLASS --> VISIT
    TOOL_CLASS --> SCHOLAR
    TOOL_CLASS --> PYTHON
    TOOL_CLASS --> FILE
    
    SEARCH --> SERPER
    VISIT --> JINA
    SCHOLAR --> SCHOLAR_API
    PYTHON --> SANDBOX
    FILE --> IDP
    
    SERPER --> SEARCH
    JINA --> VISIT
    SCHOLAR_API --> SCHOLAR
    SANDBOX --> PYTHON
    IDP --> FILE
    
    SEARCH --> AGENT
    VISIT --> AGENT
    SCHOLAR --> AGENT
    PYTHON --> AGENT
    FILE --> AGENT
```

## 4. Memory Management System

```mermaid
graph TB
    subgraph "Memory Management Architecture"
        INPUT[Input Context<br/>Messages + Tool Results]
        
        INPUT --> TOKEN_COUNT[Count Tokens<br/>AutoTokenizer/tiktoken]
        TOKEN_COUNT --> CHECK_LIMIT{Token Count > 108K?}
        
        CHECK_LIMIT -->|No| PROCESS[Process Context<br/>Normal Flow]
        CHECK_LIMIT -->|Yes| TRUNCATE[Truncate Context<br/>Preserve Recent]
        
        TRUNCATE --> PRIORITIZE[Prioritize Content<br/>Recent First]
        PRIORITIZE --> COMPRESS[Compress Older Content<br/>Summary Generation]
        COMPRESS --> PROCESS
        
        PROCESS --> REASONING[Agent Reasoning<br/>LLM Processing]
        REASONING --> TOOL_CALL[Tool Call Generation]
        TOOL_CALL --> TOOL_EXEC[Tool Execution]
        TOOL_EXEC --> RESULT[Tool Results]
        
        RESULT --> UPDATE_CONTEXT[Update Context<br/>Add to Messages]
        UPDATE_CONTEXT --> MEMORY_UPDATE[Update Memory Systems]
        
        MEMORY_UPDATE --> SUMMARY_MEM[Summary Memory<br/>Evolving Reports]
        MEMORY_UPDATE --> CONTEXT_MEM[Context Memory<br/>Conversation History]
        MEMORY_UPDATE --> OPEN_MEM[Open-world Memory<br/>Continuous Streams]
        
        SUMMARY_MEM --> NEXT_ROUND[Next Reasoning Round]
        CONTEXT_MEM --> NEXT_ROUND
        OPEN_MEM --> NEXT_ROUND
    end
```

## 5. Training Pipeline Architecture

```mermaid
graph TB
    subgraph "Training Pipeline"
        subgraph "Data Synthesis"
            SEED[Seed Data Generation<br/>ItemWriter Agent]
            ESCALATE[Iterative Complexity<br/>Escalation]
            QUALITY[Quality Control<br/>QuestionSolver + Judge]
        end
        
        subgraph "Training Stages"
            SFT[Supervised Fine-tuning<br/>RFT Cold Start]
            RL[Reinforcement Learning<br/>GRPO/DUPO/RLVR]
            CPT[Continual Pre-training<br/>Agentic CPT]
        end
        
        subgraph "Data Types"
            FAS[FAS - Planning Action<br/>Synthesis]
            HAS[HAS - Decision-Making<br/>Action Synthesis]
            SAILOR[SailorFog-QA<br/>High Uncertainty Tasks]
            ITER_DATA[Iterative Complexity<br/>Escalation Data]
        end
        
        subgraph "Optimization"
            GRPO[GRPO - Group Relative<br/>Policy Optimization]
            DUPO[DUPO - Duplicating Sampling<br/>Policy Optimization]
            RLVR[RLVR - Reinforcement Learning<br/>with Verifiable Rewards]
        end
    end
    
    SEED --> ESCALATE
    ESCALATE --> QUALITY
    QUALITY --> FAS
    QUALITY --> HAS
    QUALITY --> SAILOR
    QUALITY --> ITER_DATA
    
    FAS --> SFT
    HAS --> SFT
    SAILOR --> SFT
    ITER_DATA --> SFT
    
    SFT --> RL
    RL --> GRPO
    RL --> DUPO
    RL --> RLVR
    
    GRPO --> CPT
    DUPO --> CPT
    RLVR --> CPT
```

## 6. Multi-Agent Cooperation

```mermaid
graph TB
    subgraph "Multi-Agent Cooperation Framework"
        USER[User Query] --> ROUTER[Agent Router<br/>Task Classification]
        
        ROUTER --> GENERAL[General Research<br/>DeepResearch-30B]
        ROUTER --> WEB[Web Navigation<br/>WebSailor Models]
        ROUTER --> VISION[Vision-Language<br/>WebWatcher Models]
        ROUTER --> ITERATIVE[Iterative Research<br/>WebResearcher]
        ROUTER --> AUTONOMOUS[Autonomous Search<br/>WebDancer]
        
        subgraph "Agent Specialization"
            GENERAL --> GEN_TOOLS[Search, Visit, Scholar, Python, File]
            WEB --> WEB_TOOLS[Search, Visit, Advanced Navigation]
            VISION --> VISION_TOOLS[Image Search, OCR, Visual Analysis]
            ITERATIVE --> ITER_TOOLS[Search, Visit, Summary Generation]
            AUTONOMOUS --> AUTO_TOOLS[Search, Visit, Long-horizon Planning]
        end
        
        subgraph "Shared Resources"
            SHARED_TOOLS[Shared Tool Ecosystem<br/>Common API Access]
            SHARED_MEMORY[Shared Memory Systems<br/>Context & Summaries]
            SHARED_MODELS[Shared Model Components<br/>Base Architectures]
        end
        
        GEN_TOOLS --> SHARED_TOOLS
        WEB_TOOLS --> SHARED_TOOLS
        VISION_TOOLS --> SHARED_TOOLS
        ITER_TOOLS --> SHARED_TOOLS
        AUTO_TOOLS --> SHARED_TOOLS
        
        SHARED_TOOLS --> SHARED_MEMORY
        SHARED_MEMORY --> SHARED_MODELS
        
        subgraph "Result Aggregation"
            AGGREGATE[Result Aggregation<br/>Multi-agent Outputs]
            FUSION[Fusion Agent<br/>Last-k-fusion]
            FINAL_OUTPUT[Final Answer<br/>Synthesized Results]
        end
        
        GENERAL --> AGGREGATE
        WEB --> AGGREGATE
        VISION --> AGGREGATE
        ITERATIVE --> AGGREGATE
        AUTONOMOUS --> AGGREGATE
        
        AGGREGATE --> FUSION
        FUSION --> FINAL_OUTPUT
    end
```

## 7. Context Flow and Token Management

```mermaid
graph TD
    subgraph "Context Flow Management"
        START[Initial Query] --> INIT_CONTEXT[Initialize Context<br/>System Prompt + User Query]
        
        INIT_CONTEXT --> ROUND_START[Start Reasoning Round]
        ROUND_START --> COUNT_TOKENS[Count Current Tokens<br/>AutoTokenizer/tiktoken]
        
        COUNT_TOKENS --> CHECK_TOKENS{Tokens > 108K?}
        CHECK_TOKENS -->|No| NORMAL_FLOW[Normal Processing Flow]
        CHECK_TOKENS -->|Yes| TRUNCATE_FLOW[Truncation Flow]
        
        NORMAL_FLOW --> LLM_CALL[LLM Call<br/>Generate Response]
        LLM_CALL --> PARSE_RESPONSE[Parse Response<br/>Extract Tool Calls]
        
        PARSE_RESPONSE --> TOOL_EXECUTION[Execute Tools<br/>Get Results]
        TOOL_EXECUTION --> UPDATE_MESSAGES[Update Messages<br/>Add Tool Results]
        
        UPDATE_MESSAGES --> CHECK_COMPLETE{Answer Found?}
        CHECK_COMPLETE -->|No| ROUND_START
        CHECK_COMPLETE -->|Yes| FINAL_ANSWER[Return Final Answer]
        
        TRUNCATE_FLOW --> FORCE_ANSWER[Force Answer Generation<br/>Context Limit Reached]
        FORCE_ANSWER --> FINAL_ANSWER
        
        subgraph "Token Management Details"
            TOKEN_LIMIT[108K Token Limit<br/>Hard Constraint]
            TRUNCATION[Automatic Truncation<br/>Preserve Recent Content]
            COMPRESSION[Content Compression<br/>Summary Generation]
        end
        
        CHECK_TOKENS --> TOKEN_LIMIT
        TRUNCATE_FLOW --> TRUNCATION
        TRUNCATION --> COMPRESSION
    end
```

## 8. Error Handling and Resilience

```mermaid
graph TD
    subgraph "Error Handling and Resilience"
        REQUEST[User Request] --> VALIDATE[Validate Input]
        VALIDATE --> INIT_AGENT[Initialize Agent]
        
        INIT_AGENT --> TRY_EXECUTION[Try Execution]
        TRY_EXECUTION --> CHECK_ERROR{Error Occurred?}
        
        CHECK_ERROR -->|No| SUCCESS[Success Path]
        CHECK_ERROR -->|Yes| ERROR_HANDLER[Error Handler]
        
        ERROR_HANDLER --> RETRY_LOGIC[Retry Logic<br/>Max 10 Attempts]
        RETRY_LOGIC --> EXPONENTIAL_BACKOFF[Exponential Backoff<br/>1s, 2s, 4s, 8s, 16s, 30s]
        
        EXPONENTIAL_BACKOFF --> RETRY_ATTEMPT[Retry Attempt]
        RETRY_ATTEMPT --> CHECK_RETRY{Retry Success?}
        
        CHECK_RETRY -->|Yes| SUCCESS
        CHECK_RETRY -->|No| CHECK_MAX_RETRIES{Max Retries<br/>Reached?}
        
        CHECK_MAX_RETRIES -->|No| EXPONENTIAL_BACKOFF
        CHECK_MAX_RETRIES -->|Yes| FALLBACK[Fallback Response<br/>Error Message]
        
        SUCCESS --> RESULT[Return Result]
        FALLBACK --> RESULT
        
        subgraph "Error Types"
            API_ERROR[API Errors<br/>Network/Service Issues]
            TOOL_ERROR[Tool Errors<br/>Execution Failures]
            TIMEOUT_ERROR[Timeout Errors<br/>150min/50s Limits]
            TOKEN_ERROR[Token Errors<br/>Context Overflow]
        end
        
        ERROR_HANDLER --> API_ERROR
        ERROR_HANDLER --> TOOL_ERROR
        ERROR_HANDLER --> TIMEOUT_ERROR
        ERROR_HANDLER --> TOKEN_ERROR
    end
```

These diagrams provide a comprehensive visual representation of the DeepResearch agentic system architecture, showing how different components interact, how workflows are structured, and how the system handles various scenarios including error conditions and resource management.