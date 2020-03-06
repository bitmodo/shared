# Check if the Dangerfile was changed
has_dangerfile_changes = git.modified_files.include? "Dangerfile"
# Check if the Gemfile was changed
has_gemfile_changes = git.modified_files.include? "Gemfile"
# Check if the license file was changed
has_license_changes = git.modified_files.include? @BM_DANGER_LICENSE
# Check if the app files were changed
has_app_changes = git.modified_files.include? @BM_DANGER_APP_FILES
# Check if any of the tests were changed
has_test_changes = git.modified_files.include? @BM_DANGER_TEST_FILES
# Check if any immutable files were changed
has_immutable_changes = git.modified_files.include? @BM_DANGER_IMMUTABLE_FILES
# Check if the PR is trivial
declared_trivial = (github.pr_title + github.pr_body).include?("#trivial") || !has_app_changes

# Allow Bitmodo members to bypass some checks
unless github.api.organization_member?('bitmodo', github.pr_author)
    # Fail if the PR does not provide a description
    failure "Please provide a summary in the Pull Request description" if github.pr_body.length < 5

    # Fail if the license was modified by an external user
    failure "#{github.html_link(@BM_DANGER_LICENSE)} has been modified" if has_license_changes

    # Warn if the Dangerfile was changed by an external contributor
    warn "#{github.html_link("Dangerfile")} has been modified by external contributor" if has_dangerfile_changes

    # Warn if the Gemfile was changed by an external contributor
    warn "#{github.html_link("Gemfile")} has been modified by external contributor" if has_gemfile_changes

    # Provide a thanks message if the user is external
    message ":+1: Thank you for contributing!"
else
    # Warn if the license has been modified
    warn "#{github.html_link(@BM_DANGER_LICENSE)} has been modified" if has_license_changes

    # Provide a message if the user is internal
    message ":ok_hand:"
end

# Warn if immutable files have been changed
fail "Immutable files have been changed" if has_immutable_changes
# Warn if the app files have changes but the tests do not
warn "Tests have not been updated" if has_app_changes && !has_test_changes && !declared_trivial
# Create a warning if the PR is a work in progress
warn "PR is classed as Work in Progress" if github.pr_title.include? "[WIP]"
# Warn if the PR is not assigned to anyone
warn "This PR does not have any assignees yet." unless github.pr_json["assignee"]
# Warn if the PR is not mergeable
warn "This PR cannot be merged yet" unless github.pr_json["mergeable"]
