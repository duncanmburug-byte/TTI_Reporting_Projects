# KENYA GRADUATION FORM-2026
# Church Plant Reporting: building the cascading Kobo form

How to load the corrected form into KoboToolbox so that **Training Center Group** filters **Paul**, and **Paul** filters **Timothy**. It also lists what was wrong with the original files.


## Cannot read properties of null error

The message `Cannot read properties of null (reading 'textContent')` appears when the web form asks a list for a `label` and the list has none. Kobo reads every `select_one_from_file` CSV using two fixed columns, `name` and `label`. Your CSVs used `paul_name`, `timothy_name` and `titus_name` instead, so Kobo found no label to show.

| Where | Problem | Fix |
| --- | --- | --- |
| kobo_pauls.csv kobo_timothies.csv kobo_tituses.csv | No `name` / `label` columns. This causes the error in your screenshot. | Added `name` and `label` (both the person's name). Kept `training_center_group` and `paul_name` as filter columns. |
| All four CSVs | Double spaces in 116 names and in every `Coast - … Region (East Africa…)` group, which breaks exact-match filtering when a value is typed or edited elsewhere. | Collapsed to single spaces everywhere, the same way in every file, so parent and child values still match. |
| survey: titus_1, titus_2 | No filter, so all 5,865 Tituses load. 83 names belong to more than one Paul. | Filtered by group and Paul, the same pattern as Timothy. |
| survey: titus_2 | Required, but 7 Pauls have exactly one Titus and 67 have none. | Made optional. |
| survey: phone_number | Regex had a doubled backslash (`\\+254`), so valid numbers failed. | `regex(., '^(\+254\|254\|0)[0-9]{9}$')` |
| survey: submission_key | `required` set on a calculate row. | Removed. |
| settings | Version 1. | Version 2, so Kobo treats it as a new deployment. |

Checked: every filter column exists in its CSV, every Paul and Timothy points to a valid parent, and there are no duplicate names inside a parent. I could not run the form through Kobo itself, so the test in step 9 is the final proof.

**Please check.** If a Paul has no Titus, the Titus questions have nothing to pick. Answer "No" to "Was there a Titus involved?" in that case. A few names contain a comma, semicolon or backslash (for example `Muthung;u`, `\M'nceene`). They look like typing slips. I left them as they were.

## How the cascade works

Question 1

**training_group**\
Choose from the 8 groups

**→**

Question 2

**paul_name**\
Only Pauls where\
`training_center_group = group`

**→**

Question 3

**timothy_name**\
Only Timothies where group and Paul match

The text to the left of `=` is a column in the CSV. The `${…}` on the right is the answer already given on the form.

| type | name | choice_filter |
| --- | --- | --- |
| select_one_from_file training_groups.csv | training_group | (none) |
| select_one_from_file kobo_pauls.csv | paul_name | training_center_group=${training_group} |
| select_one_from_file kobo_timothies.csv | timothy_name | training_center_group=${training_group} and paul_name=${paul_name} |
| select_one_from_file kobo_tituses.csv | titus_1, titus_2 | training_center_group=${training_group} and paul_name=${paul_name} |

Column layout of the corrected files:

```
training_groups.csv   name,label
kobo_pauls.csv        name,label,training_center_group
kobo_timothies.csv    name,label,training_center_group,paul_name
kobo_tituses.csv      name,label,training_center_group,paul_name
```

## STEP 1 Sign in and start a new project

Go to your Kobo server (for example `kf.kobotoolbox.org`), sign in, and click **+ NEW** at the top left.

https://kf.kobotoolbox.org/#/forms

KoboToolbox

\+ NEWSearch my projects

My existing projectsDeployed · 0 submissions

The project list. The orange outline marks the button to click.

## STEP 2 Choose "Upload an XLSForm"

In the dialog that opens, pick **Upload an XLSForm**.

#### Create project

Build from scratch

**Upload an XLSForm**

Import an XLSForm via URL

Create project dialog.

## STEP 3 Select church_plant_reporting_fixed.xlsx

Drag `church_plant_reporting_fixed.xlsx` onto the box, or click it to browse. Upload only the .xlsx here. The CSV files go in at step 5.

#### Upload an XLSForm

Drag and drop an XLSForm file here, or click to browse

church_plant_reporting_fixed.xlsx*ready*

Cancel

Dropping the XLSForm file.

## STEP 4 Fill in the project details

Kobo reads the form title from the file. Add a country and sector if your organisation uses them, then click **Create project**.

#### Project details

Project Name

Church Plant Reporting

Description

East Africa 25-27 church plant reports

Country

Kenya

Sector

Select…

BackCreate project

Project details dialog.

If Kobo reports a warning here, read it. A warning that mentions a missing file is expected until step 5.

## STEP 5 Upload the four CSV files as media

Open the project, go to the **FORM** tab and click the pencil icon to open the form builder. Click the gear icon **Layout & Settings**, open **Media** and upload these four files:

- `training_groups.csv`
- `kobo_pauls.csv`
- `kobo_timothies.csv`
- `kobo_tituses.csv`

The file names must match the XLSForm exactly, including lower case. In newer Kobo versions the same upload is under project **SETTINGS → Media**.

Church Plant ReportingLayout & Settings

**Form style****MEDIA**

Drop CSV files here or click to upload

training_groups.csv*uploaded*

kobo_pauls.csv*uploaded*

kobo_timothies.csv*uploaded*

kobo_tituses.csv*uploaded*

Media tab with all four files uploaded.

## STEP 6 Check the questions in the form builder

Close the settings panel and look at the question list. You should see the three cascading questions in order, then phone number, the church questions and the Titus questions. Click **Save** if you changed anything.

Church Plant ReportingPreviewSAVE

Training Center GroupSelect One from file

Name of PaulSelect One from file

Name of TimothySelect One from file

Phone NumberText

Planted a church?Select One

Form builder question list.

The form builder does not let you add a "select from file" question by clicking. That is why this form is built in the spreadsheet and uploaded. To change a filter later, edit the choice_filter cell in Excel and upload the file again as a replacement.

## STEP 7 Deploy

Back on the project page, open the **FORM** tab and click **DEPLOY**. Confirm the dialog. For an already deployed project, the button says **REDEPLOY** and must be used after every change to the form or the CSV files.

Church Plant Reporting

**SUMMARY****FORM****DATA****SETTINGS**

DEPLOYEditReplace form

Draft · version 2 · 14 questions

The FORM tab before deployment.

## STEP 8 Open the form in the web form

After deploying, open the **FORM** tab, click **Collect data** (the **Online-Offline (multiple submission)** link) and open it in a new tab. This is the same page that showed your error, so a successful load means the fix worked.

https://ee.kobotoolbox.org/x/…

**Church Plant Reporting**

\* Training Center Group

Type to search…▾

**No "Loading Error" dialog appears.**

The form opens without the error dialog.

## STEP 9 Test the cascade

1. Choose **Coast - Central Region (East Africa 25-27)**.
2. Open **Name of Paul**. Only that group's Pauls (66 of the 383) are listed.
3. Choose **Moses Ngonyo Sirya**. Open **Name of Timothy**. Only his Timothies appear.
4. Go back and change the group. Paul and Timothy clear and the lists shrink to the new group.

Training Center Group

Coast - Central Region (East Africa 25-27)▾

Name of Paul

Moses Ngonyo Sirya▾

Name of Timothy

mo…▾

Julius Katana Mwamure

Maurin Sidi Charo

Francis Sirya Ngala

The Timothy list shows only people under the chosen Paul. Names are taken from your file.

Two more checks. Answer **Yes** to "Planted a church?" and confirm the church questions appear. Enter `0712345678` and confirm the phone number is accepted.

## If the error comes back

| Symptom | Likely cause | What to do |
| --- | --- | --- |
| `textContent` loading error | An old CSV is still attached, or a CSV lacks `name` or `label`. | Delete the old media files, upload the four corrected files, then REDEPLOY. |
| Paul list empty | Group value in `kobo_pauls.csv` does not match the group `name` exactly. | Use the files supplied. Do not edit names by hand in one file only. |
| Change not visible | Form not redeployed. | Click REDEPLOY, then reload the form page. |
| Slow typing in lists | Large lists (6,158 Timothies) on an old phone. | This is expected with a long list. The filters keep each visible list short. |