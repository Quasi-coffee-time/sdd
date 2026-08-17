# 什么是Spec

Spec包含以下主要内容
* 定义问题  
* 成功条件  
* 约束条件  
* 用户流程  
基于Spec的开发最主要的就是写好spec，以及怎么利用spec引导agent做开发。本文基于deeplearning.ai的公开课（https://www.deeplearning.ai/courses/spec-driven-development-with-coding-agents），总结了SDD开发方法，包括定义、prompt、开发流程等内容。
# 基于Spec的开发过程

- 关键点1：整个开发过程基于“宪法（Constitution）” ，定义项目**任务、技术栈、路线图**等
- 关键点2：整个开发过程需要把每个特性点定义清楚，包含**规格、实施方案、验证方案**
- 关键点3：整个开发过程中，”开发计划“是会迭代的
![Constitution](Attachments/657A7F10-D49A-4B73-BA54-5CD99EC819DC.png)  

# Constitution

## 什么是constitution “宪法”

constitution由3份文档组成
- mission 任务
- tech stack 技术栈
- roadmap 开发路线图
![[Pasted image 20260817230256.png]]  

## 生成constitution的prompt怎么写  

```
Let's create a "constitution" in a specs directory:  
- `mission.md`  
- `tech-stack.md`  
- `roadmap.md` for high-level implementation order, in very small phases of work.  
  
Important: You **must** use your **AskUserQuestion** tool, grouped on these 3, before writing to disk.  
```

- 关键点1：包含mission、tech-stack、roadmap  
- 关键点2：roadmap是高层次的实施顺序，每一个步骤分成小的工作阶段  

这个过程把想做什么、受众是谁描述清楚，agent会通过问答方式引导用户把这三份文档写好。

# 特性定义

## 什么是feature spec

constitution里只会定义一些高层次的规范，具体开发细节需要分解成feature进行开发。每个feature的开发spec由3份文档构成，分别是
- plan
- requirements
- validation

## 生成feature的3份文档的prompt怎么写 
```
Find the next phase on specs/roadmap.md and make a branch, ask me about the feature spec.  

Create:  

- A new directory YYYY-MM-DD-feature-name under specs for this feature work  

- In there:  

- `plan.md` as a series of numbered task groups.  

- `requirements.md` for the scope, decisions, context  

- `validation.md` for how to know the implementation succeeded and can be merged  
 
Refer to specs/mission.md and specs/tech-stack.md for guidance.  
  
Important: You **must** use your AskUserQuestion tool, grouped on these 3, before writing to disk.  
```

- 关键点1：根据roadmap，LLM分析出下一步应该开发哪个feature
- 关键点2：git管理，make a branch  
- 关键点3：每个特性一个文件夹，包含时间信息  
- 关键点4：plan是带编号的任务组，即结构化的任务清单  
- 关键点5：requirements包含范围（这个项目/功能做什么和不做什么）、决策（在开发过程中做了哪些关键选择以及为什么）、背景（为什么要做这个项目？当前环境是什么？有哪些约束条件？）  
- 关键点6：validation用于记录如何判断实现是成功的、可以合并代码的    


# Spec文件结构
```
specs/
│
├── mission.md        
├── roadmap.md        
├── tech-stack.md     
│
└── 2026-03-30-feature1/
    ├── requirements.md   
    ├── plan.md           
    └── validation.md     
```


# SDD开发流程

1. 创建constitution （3份文档）
2. Engineer-in-loop：工程师需要看一下文档的描述是否符合要求，不对的地方指出来让agent修改，constituition没有问题再进行下一步
3. 创建第一个feature spec（3分文档）
4. Engineer-in-loop：工程师需要看一下文档的描述是否符合要求，不对的地方指出来让agent修改，spec没有问题再进行下一步
5. spec没有问题，可以让agent提交一下git ，prompt:  `commit the changes`
6. 让agent实现，prompt: `实现这个特性`
7. Engineer-in-loop：工程师手动验证功能可以实现，并且可以让agent反思是否有问题，如果有问题，则让agent修改。prompt: 
```
Do a deep review: Spawn multiple subagents to go through all the changes on this branch from three different perspectives and see if anything doesn't make sense, could be better, etc.   
```
8.  都没有问题，可以提交这个特性到git，prompt:
```
commit this, switch to main, and merge this branch, then delete it
```
9. 第一个feature开发完后，再循环3~8开发下一个feature
# 特性spec撰写的skill化

需要开发的feature撰写spec的过程，可以总结为skill，prompt：
```
I want to stop repeating the feature spec prompt. Use your skill creator to help me write a "feature spec" local skill. Here is the previous prompt:  
 
Find the next phase on specs/roadmap.md and make a branch, ask me about the feature spec.  

Create:  

- A new directory YYYY-MM-DD-feature-name under specs for this feature work  

- In there:  

- `plan.md` as a series of numbered task groups.  

- `requirements.md` for the scope, decisions, context  

- `validation.md` for how to know the implementation succeeded and can be merged  
  
Refer to specs/mission.md and specs/tech-stack.md for guidance.  
 
Important: You **must** use your AskUserQuestion tool, grouped on these 3, before writing to disk.  
```

- 关键点1：use skill creator  
- 关键点2：把skill的过程写出来，并告知与我讨论feature spec ，使用的是AskUserQuestion tool

后面再需要生成feature的spec（3份文档）时，直接调用这个skill即可。


# 已有项目怎么进行SDD开发

原则也很简单，先让agent根据代码、项目简单介绍README.md、想要做的开发`TODO.md`整理出constitution，prompt：
```
We have an xxx project, 简单介绍下这个项目.

Look in README.md, make a constitution in a specs directory:

- `mission.md`

- `tech-stack.md`

- `roadmap.md` should be based on the TODO.md for high-level implementation order, in very small phases of work.


Interview me about mission, target audience, tech stack gaps.
  

Important: You *must* use your AskUserQuestion tool, grouped on these 3, before writing to disk.
```

后面开发流程就一样了