# Foo's Wedding Playlist

A one-page voting site for the wedding band setlist. Visitors type a name and
upvote or downvote any of the 187 songs. Scores are shared and update live.

## Setup (about 10 minutes)

### 1. Create the Firebase project
1. Go to <https://console.firebase.google.com> and create a project.
   Turn Google Analytics off — it isn't needed.
2. In the left sidebar choose **Build → Firestore Database → Create database**.
   Pick **Start in production mode** and any region (asia-southeast1 is closest
   to Bangkok; it makes no practical difference at this size).
3. Stay on the **Spark** plan. No card required.

### 2. Add a web app and copy the config
1. Project settings (gear icon) → **General** → scroll to *Your apps* →
   click the **web** icon `</>`.
2. Register the app. Skip Firebase Hosting.
3. Copy the `firebaseConfig` values into `config.js` in this folder.

These values are public by design — they identify the project, they don't
grant access. The security rules below are what actually protect the data.

### 3. Publish the security rules
Firestore Database → **Rules** tab → replace everything with the contents of
`firestore.rules` → **Publish**.

This allows anyone to read all votes and write a voter document, and blocks
everything else in the project.

### 4. Put it on GitHub Pages
```
git init
git add index.html config.js README.md firestore.rules
git commit -m "Wedding playlist voting"
git branch -M main
git remote add origin https://github.com/<you>/<repo>.git
git push -u origin main
```
Then in the repo: **Settings → Pages → Source: Deploy from a branch →
main / (root) → Save**. The site appears at
`https://<you>.github.io/<repo>/` within a minute or two.

## How it works

- Each voter gets one document in the `voters` collection holding their name
  and their votes.
- Returning with the same name — even on another device — finds the existing
  document and loads those votes, so people edit rather than duplicate.
- **Not you?** clears the local identity so a shared laptop can pass to someone else.
- If Firestore is unreachable the page still works; votes save to the device
  and a banner explains what happened.

## Limits

Firestore's free tier allows 50,000 reads and 20,000 writes per day and 1 GiB
stored. A dozen friends voting comes nowhere near this.

Anyone with the link can vote, and names aren't verified — fine among friends,
but don't treat the result as a secure ballot.

## Changing the song list

The songs are embedded in `index.html` as the `DATA` array: each entry is
`[title, artist, bits]`, where bits are 1 = band's list, 2 = Kasten's playlist,
4 = common wedding song. Votes are stored against the array *index*, so
reordering or removing songs will shift existing votes. Add new songs at the
end, or clear the `voters` collection and start fresh.
