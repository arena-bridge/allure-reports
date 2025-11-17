# Allure reports integrated functionality

Repository for storing reports

https://arena-bridge.github.io/allure-reports/#


Step by step Instruction for creating an allure-reports on Github Pages functionality, for storing automatically reports from test runs.

1. Create a Personal Access Token in
   GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)

   Select:
   
      repo(full access)
   
      workflow

3. Add the token to the private repository from where the report and artifacts will be generated.

   Repository → Settings → Secrets and variables → Actions → "New repository secret"
   
   Call it: REPORTS_DEPLOY_TOKEN
   
4. In GitHubs Pages in repository setting set up accordingly:

 Pages Source: 

    Deploy from a branch 

 Branch:

    gh-pages / root

5. Add the given steps to the end of the .yml file:
   
   
       name: Deploy to public GitHub Pages repository
        if: always()
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.REPORTS_DEPLOY_TOKEN }}
          external_repository: your-username/your-public-reports-repo   ⬅️ Change to yours
          publish_dir: ./allure-report       ⬅️ Change to yours
          publish_branch: gh-pages
          keep_files: true
          user_name: 'github-actions[bot]'
          user_email: 'github-actions[bot]@users.noreply.github.com'
          commit_message: 'Deploy test report from workflow run #${{ github.run_number }}'

6. The allure reports will be accesible from the domain visible in Pages section. 
   


