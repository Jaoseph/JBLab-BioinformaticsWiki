# AIR, **A**nother **I**ndenter for **R**

##### *"Writing R code without AIR is like turning in a thesis written in crayon."* The ideas might be brillant but no one's going to take them seriously until you clean it up.

---

## **What is AIR?**

---

AIR, which stands for *Another Indenter for R* is a strict R code formatter that helps users:

- **Automatically enforce consistent code layout and indentation**

This way your scripts will be perfectly readable to anybody! 

> *Look at you, what a good researcher* 😉

---

## **Let's set it up in your RStudio**

#### 1) Open your Windows PowerShell terminal

Firstly, open up your windows powershell terminal. 

#### 1) Install AIR on Windows

Copy the code below and run it on your windows powershell terminal.

```powershell
powershell -ExecutionPolicy Bypass -c "irm https://github.com/posit-dev/air/releases/latest/download/air-installer.ps1 | iex"
```

#### 2) Locate the path to air.exe 

Let's find and **copy** the path to the AIR software (*air.exe*)

```powershell
Get-Command air
```

The path to AIR should resemble the example shown:
`C:\Users\chong02\.local\bin\air.exe`

#### 3) Open your RStudio

Next, we will look to configure your RStudio to run AIR

#### 4) Rstudio Configuration

- At the top, Under `Tools`, select `Global Options`.

- In the options window, select `Code` on the left sidebar, and choose the `Formatting` tab

- Under `Code formatter:`, select the option `External`

- Once External has been selected, under `Reformat command`, paste the path to AIR you had previously copied in **Step 2**.

- Click `Apply`

- Next, select the `Saving` tab and check the `Reformat documents on save`

- Click `Apply` and `OK` and restart RStudio



## **Using AIR in RStudio**

Two ways of using AIR

##### - As you work

Highlight your code and run `ctrl+shift+A`

##### - When you save

Hit `ctrl+shift+s` to save. AIR should automatically re-format your code.

> Your script is now shareable! 