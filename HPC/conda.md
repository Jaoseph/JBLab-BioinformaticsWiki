# Conda - A Recipe for Reproducible Research

##### *"Working without Conda is like baking without measuring cups."* Sure, you can do it, but don’t expect it to turn out the same twice.

---

## **What the \*\*\*\* is Conda?**

---

In **Computer Science terms**, **Conda** is a *package and environment manager* that helps users:

- **Install** software and packages,
- **Manage** different tools and dependencies,
- **Reproduce** the exact same setup across different computers or collaborators.

Understood? Perfect.  
Didn’t understand? Yikes.

Just Kidding.

---

### 🥘 The Kitchen Analogy

Let’s make it simple.

Think of **Conda** as your **kitchen manager**.

You use Conda to create **isolated kitchens** (called *environments*), where each kitchen contains:

- specific **ingredients** (*packages*),
- specific **cooking tools** (*software versions*),
- tailored for a **specific recipe** (*your project*, like *scRNA analysis* maybe?).

That way, when you're ready to cook (*run your project*), you already have **exactly what you need**:

- **Pan vs Pot ?** → *R vs Python* 
- **Overnight or Fresh rice ?** → *different package versions*
- **Need Ginger and Garlic ?** → *Tidyverse and Readxl*


> *Note: YOU'LL NEVER COOK WITH NON FRESH INGREDIENTS UNLESS YOU WANT TO.*

You've prepped your kitchen to a tee.

---

### Why should I care?

You don't *need* to. 
**But you should.** And here's why:

1. It **reduces the chance of version or installation errors**, especially on **High Performance Clusters (HPC)** 

    Where fixing these can be… **traumatic.**
   *(Solving these gave me nightmares too. Trust me...)*

2. It makes your work **reproducible** and anyone using the same kitchen can cook the **same dish** (aka produce the same results).

> *Look at you, what a good researcher* 😉

---

Now that you’ve had a taste of what Conda is and why it matters,  
let’s walk through how to **set up your Conda kitchen**,  
along with some **tips & tricks** to make it painless.

> *Note: You’ll definitely want to set this up in the HPC. On your personal computer, it’s optional but still helpful for reproducibility. (Jao: IF YOU REALLY WANT TO ...)*

--- 

## **Let's set up your dream kitchen**

---

### Installing Conda *(Your kitchen manager)*

---

First things first, we need to the app that manages your kitchen. This will help you organize your ingredients, tools, and recipes.

There are a few different versions of Conda out there:

- **Anaconda** (comes with lots of packages — a bit heavy)
- **Miniconda** (lighter, more customizable)
- **Mamba** (a faster alternative)

But to keep things **simple and clean**, we’ll go with **Miniforge**.

It’s:

- easy to install  
- great for both **R** and **Python**  
- and most importantly... **simple**

---

### Steps to Install Miniforge 

#### 1) Go to your home directory *(i.e. your house)*

```powershell
cd ~
```
#### 1) Start by downloading it Miniforge App from the internet
```powershell
wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
```

*Works on the HPC but not CRUK local laptops*

#### 2) Install the downloaded Miniforge

```powershell
bash Miniforge3-Linux-x86_64.sh
```

Note: It will pop-up saying "Miniforge3 will now be installed into this location: /Users/{YOUR CRUK ID}/miniforge3". Just accept your default location fate.

#### 3) Delete the Miniforge installer 

After you finish installing, you can delete the installer. Makes your working space just cleaner (please do your chores)

```powershell
rm Miniforge3-Linux-x86_64.sh
```

#### 4) INITIATE MINIFORGE

```powershell
conda init
```

#### 5) Go back to your scratch

```powershell
cd /mnt/scratchc/jblab/{directory_name}/
```
For example: replace {directory_name} with chong02 or middle01


#### 🎉🎉 YOU'VE INSTALLED YOUR KITCHEN MANAGER 🎉🎉 

Let's start building your dream kitchen now.

---

### 

##### `Coming soon, Work in progress 👷‍♂️`

