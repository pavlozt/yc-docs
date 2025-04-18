# Working with dialogs

In a [dialog](../../concepts/dialogs.md), you can listen to an audio of the conversation or view the text chat between a customer and an agent or bot. You can also get the related information, e.g., whether the agent was able to help the customer in the course of the conversation.

## Viewing a list of dialogs {#list-dialogs}

1. Open the {{ speechsense-name }} [home page]({{ link-speechsense-main }}).
1. Go to the space you need and select a project.

By default, the **{{ ui-key.yc-ui-talkanalytics.dialogs.dialogs }}** tab shows 100 most recent dialogs. To see the previous dialogs, click **{{ ui-key.yc-ui-talkanalytics.common.show-more }}** at the end of the list.

To search for dialogs, you can use [filters](#filters-dialogs) and the [search bar](#find-dialogs).

## Finding dialogs using filters {#filters-dialogs}

1. Open the {{ speechsense-name }} [home page]({{ link-speechsense-main }}).
1. Go to the space you need and select a project.
1. In the **{{ ui-key.yc-ui-talkanalytics.dialogs.dialogs }}** tab, click ![image](../../../_assets/console-icons/plus.svg) **{{ ui-key.yc-ui-talkanalytics.common.add-filter }}**.
1. Select the required filter and enter its value.
1. Click **{{ ui-key.yc-ui-talkanalytics.common.apply }}**.

You can add multiple filters. They will be combined by the logical `AND` operation to find the dialogs satisfying all the specified conditions.

## Finding dialogs by text fragment {#find-dialogs}

Full-text search allows you to find dialogs containing a specific word or phrase. The better the dialog text matches the search query and the more frequently it occurs, the higher is the dialog placed in the search results.

To perform a search:

1. Open the {{ speechsense-name }} [home page]({{ link-speechsense-main }}).
1. Go to the space you need and select a project.
1. On the **{{ ui-key.yc-ui-talkanalytics.dialogs.dialogs }}** tab, enter the desired text fragment in the search bar.
1. (Optional) To the right of the search bar, select the channel you want to search from the drop-down list: **{{ ui-key.yc-ui-talkanalytics.dialogs.client }}** or **{{ ui-key.yc-ui-talkanalytics.dialogs.operator }}**.

## Viewing a dialog {#view-dialog}

1. Open the {{ speechsense-name }} [home page]({{ link-speechsense-main }}).
1. Go to the space you need and select a project.
1. Find the required dialog using [filters](#filters-dialogs) and the [search bar](#find-dialogs).
1. Open the dialog in one of the following ways:

    * To open a dialog preview, click the dialog line.
    * To open the dialog in a new window, click ![image](../../../_assets/console-icons/arrow-up-right-from-square.svg) on the right in the dialog line.

The dialog page displays the following information:

* Metadata such as agent and customer names, call or message date, dialog language.
* Conversation audio (only for audio).
* Conversation contents on the **{{ ui-key.yc-ui-talkanalytics.dialogs.dialog }}** tab.
* Semantic analysis of the conversation on the **{{ ui-key.yc-ui-talkanalytics.projects.sumarization }}** tab.

    The tab contains several sections. If you want to leave only a specific section, click on it.

To learn more, see [{#T}](../../concepts/dialogs.md#details).

## Example of searching dialogs by time period and text fragment {#example-find-dialogs-by-period-and-text}

Search criteria:

* Period: `10.11.2023 - 16.11.2023`.
* Contains the customer's utterance: `good service`.

Follow these steps:

1. Open the {{ speechsense-name }} [home page]({{ link-speechsense-main }}).
1. Go to the space you need and select a project.
1. In the **{{ ui-key.yc-ui-talkanalytics.dialogs.dialogs }}** tab, enter `good service` in the search bar.
1. To the right of the search bar, select the **{{ ui-key.yc-ui-talkanalytics.dialogs.client }}** channel from the drop-down list.
1. Click ![image](../../../_assets/console-icons/plus.svg) **{{ ui-key.yc-ui-talkanalytics.common.add-filter }}**.
1. In the **{{ ui-key.yc-ui-talkanalytics.dialogs.common-metadata }}** group, select the **Start date** filter. As the filter value, specify the `10.11.2023 - 16.11.2023` date range.
1. Click **{{ ui-key.yc-ui-talkanalytics.common.apply }}**.

The filtered dialogs will be displayed as a result.

Example of ranking dialogs depending on the found text fragments:

#|
|| **No.** | **Dialog date** | **Dialog fragment** ||
|| 1 | 16.11.2023 | thank you for `good service` ||
|| 2 | 13.11.2023 | `good` equipment and `service` ||
|| 3 | 15.11.2023 | full system `service` ||
|#
