<svelte:head>
  <title>Contact me</title>
</svelte:head>

<form action="mailto:foo@example.com" method="GET">
    <label for="email">Email: <input id="email" type="email" name="text_email"> </label>
    <label for="subject">Subject: <input id="subject" type="text" name="text_subject"> </label>
    <label for="body">Body: <textarea id="body" name="text_message"></textarea> </label>

    <input type="submit" value="Submit" id="button">
    <!-- <button>Submit</button> -->
</form>