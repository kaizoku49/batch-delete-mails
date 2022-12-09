# batch-delete-mails
/*
This script, when used with Google Apps Scripts, will delete 400 emails and
can be triggered to run every few minutes without user interaction enabling you
to bulk delete email in Gmail without getting the #793 error from Gmail.
Google returns a maximum of 500 email threads in a single API call. 
This script fetches 400 threads in case 500 threads is causing timeouts
Configure the search query in the code below to match the type of emails
you want to delete
See - https://developers.google.com/apps-script/reference/gmail/gmail-app#search(String)
and https://support.google.com/mail/answer/7190
Browse to https://script.google.com/
Start a script and paste in the code below. 
After you paste it in, save it. In the drop down at the top select the function you want
to run. For example, you could run the batchDeleteEmail function.
This gist contains a few different functions to give examples of how to do other actions
besides deleting emails. For example if you wanted to mark all mail with the "work" label as read
you could run the markReadLabelWork function
Next click the little clock looking button.
This is for your triggers. You can set up how frequently you want the script
to run (I did mine for every minute but others are seeing execution take longer than
a minute in which case you may want to run every 5 or 15 minutes).
This writeup from @timur-tabi goes into more detail : https://docs.google.com/document/d/1PLfAnNus-B87gHS1pkbmzFTkWckAPNcqmvO7hFo_gBc/edit
Source : # https://productforums.google.com/d/msg/gmail/YeQVDuPIQzA/kpZPDDj8TXkJ
This gist includes additions by @kulemantu found in their fork : https://gist.github.com/kulemantu/84682cfebe72eb925cfe/revisions
*/

function batchDeleteEmail() {
  processEmail('label:inbox from:user@example.com', 'moveThreadsToTrash');
}

function markReadLabelWork() {
  processEmail('label:work', 'markThreadsRead');
} 

function markReadFromInfoExample() {
  processEmail('from:user@example.com', 'markThreadsRead');
}

function processEmail(search, batchAction) {
  var batchSize = 100; // Process up to 100 threads at once
  var searchSize = 400; // Limit search result to a max of 400 threads. Use this if you encounter the "Exceeded maximum execution time" error

  var threads = GmailApp.search(search, 0, searchSize);
  for (j = 0; j < threads.length; j += batchSize) {
    GmailApp[batchAction](threads.slice(j, j + batchSize));
  }
}
