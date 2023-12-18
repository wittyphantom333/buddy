# Changelog

## v1.11.8
### Changed
* Release binaries are now built with Go 1.20.10 (resolves [CVE-2023-44487](https://www.cve.org/CVERecord?id=CVE-2023-44487))
* Updated Go dependencies

## v1.11.7
### Changed
* Updated Go dependencies (this resolves an issue related to `http: invalid Host header` with Docker)
* Buddy is now built with go1.19.11

## v1.11.6
### Fixed
* CVE-2023-32080

## v1.11.5
### Added
* Added a config option to disable Buddy config.yml updates from the Panel (https://github.com/pteranodon/buddy/commit/ec6d6d83ea3eb14995c24f001233e85b37ffb87b)

### Changed
* Buddy is now built with Go 1.19.7

### Fixed
* Fixed archives containing partially matched file names (https://github.com/pteranodon/buddy/commit/43b3496f0001cec231c80af1f9a9b3417d04e8d4)

## v1.11.4
### Fixed
* CVE-2023-25168

## v1.11.3
### Fixed
* CVE-2023-25152

## v1.11.2
### Fixed
* Backups being restored from remote storage (s3) erroring out due to a closed stream.
* Fix IP validation logic for activity logs filtering out valid IPs instead of invalid IPs

## v1.11.1
### Changed
* Release binaries are now built with Go 1.18.10
* Timeout when stopping a server before a transfer begins has been reduced to 15 seconds from 1 minute
* Removed insecure SSH protocols for use with the SFTP server

### Fixed
* Unnecessary Docker client connections being left open, causing a slow leak of file descriptors
* Files being left open in parts of the server's filesystem, causing a leak of file descriptors
* IPv6 addresses being corrupted by flawed port stripping logic for activity logs, old entries with malformed IPs will be deleted from the local SQLite database automatically
* A server that times out while being stopped at the beginning of a transfer no longer causes the server to become stuck in a transferring state

## v1.11.0
### Added (since 1.7.2)
* More detailed information returned by the `/api/system` endpoint when using the `?v=2` query parameter.

### Changed (since 1.7.2)
* Send re-installation status separately from installation status.
* Buddy release versions will now follow the major and minor version of the Panel.
* Transfers no longer buffer to disk, instead they are fully streamed with only a small amount of memory used for buffering.
* Release binaries are no longer compressed with UPX.
* Use `POST` instead of `GET` for sending the status of a transfer to the Panel.

### Fixed (since 1.7.2)
* Fixed servers outgoing IP not being updated whenever a server's primary allocation is changed when using the Force Outgoing IP option.
* Fixed servers being terminated rather than gracefully stopped when a signal is used to stop the container rather than a command.
* Fixed file not found errors being treated as an internal error, they are now treated as a 404.
* Buddy can be run with Podman instead of Docker, this is still experimental and not recommended for production use.
* Archive progress is now reported correctly.
* Labels for containers can now be set by the Panel.
* Fixed servers becoming deadlocked when the target node of a transfer goes offline.

## v1.11.0-rc.2
### Added
* More detailed information returned by the `/api/system` endpoint when using the `?v=2` query parameter.

### Changed
* Send reinstallation status separately from installation status.

### Fixed
* Fixed servers outgoing IP not being updated whenever a server's primary allocation is changed when using the Force Outgoing IP option.
* Fixed servers being terminated rather than gracefully stopped when a signal is used to stop the container rather than a command.
* Fixed file not found errors being treated as an internal error, they are now treated as a 404.

## v1.11.0-rc.1
### Changed
* Buddy release versions will now follow the major and minor version of the panel.
* Transfers no longer buffer to disk, instead they are fully streamed with only a small amount of memory used for buffering.
* Release binaries are no longer compressed with UPX.

### Fixed
* Buddy can be run with podman instead of Docker, this is still experimental and not recommended for production use.
* Archive progress is now reported correctly.
* Labels for containers can now be set by the Panel.

## v1.7.5
### Fixed
* CVE-2023-32080

## v1.7.4
### Fixed
* CVE-2023-25168

## v1.7.3
### Fixed
* CVE-2023-25152

## v1.7.2
### Fixed
* The S3 backup driver now supports Cloudflare R2

### Added
* During a server transfer, there is a new "Archiving" status that outputs the progress of creating the server transfer archive.
* Adds a configuration option to control the list of trusted proxies that can be used to determine the client IP address.
* Adds a configuration option to control the Docker username space setting when Buddy creates containers.

### Changed
* Releases are now built using `Go 1.18` — the minimum version required to build Buddy is now `Go 1.18`.

## v1.7.1
### Fixed
* YAML parser has been updated to fix some strange issues

### Added
* Added `Force Outgoing IP` option for servers to ensure outgoing traffic uses the server's IP address
* Adds an option to control the level of gzip compression for backups

## v1.7.0
### Fixed
* Fixes multi-platform support for Buddy' Docker image.

### Added
* Adds support for tracking of SFTP actions, power actions, server commands, and file uploads by utilizing a local SQLite database and processing events before sending them to the Panel.
* Adds support for configuring the MTU on the `pteranodon0` network.

## v1.6.4
### Fixed
* Fixes a bug causing CPU limiting to not be properly applied to servers.
* Fixes a bug causing zip archives to decompress without taking into account nested folder structures.

## v1.6.3
### Fixed
* Fixes SFTP authentication failing for administrative users due to a permissions adjustment on the Panel.

## v1.6.2
### Fixed
* Fixes file upload size not being properly enforced.
* Fixes a bug that prevented listing a directory when it contained a named pipe. Also added a check to prevent attempting to read a named pipe directly.
* Fixes a bug with the archiver logic that would include folders that had the same name prefix. (for example, requesting only `map` would also include `map2` and `map3`)
* Requests to the Panel that return a client error (4xx response code) no longer trigger an exponential backoff, they immediately stop the request.

### Changed
* CPU limit fields are only set on the Docker container if they have been specified for the server — otherwise they are left empty.

### Added
* Added the ability to define the location of the temporary folder used by Buddy — defaults to `/tmp/pteranodon`.
* Adds the ability to authenticate for SFTP using public keys (requires `Panel@1.8.0`).

## v1.6.1
### Fixed
* Fixes error that would sometimes occur when starting a server that would cause the temporary power action lock to never be released due to a blocked channel.
* Fixes a bug causing the CPU usage of Buddy to get stuck at 100% when a server is deleted while the installation process is running.

### Changed
* Cleans up a lot of the logic for handling events between the server and environment process to make it easier to make modifications to down the road.
* Cleans up logic handling the `StopAndWait` logic for stopping a server gracefully before terminating the process if it does not respond.

## v1.6.0
### Fixed
* Internal logic for processing a server start event has been adjusted to attach to the Docker container before attempting to start the container. This should fix issues where a server would get stuck after pulling the container image.
* Fixes a bug in the console output that was dropping console lines when a large number of lines were sent at once.

### Changed
* Removed the console throttle logic that would terminate a server instance that was sending too much data. This logic has been replaced with simpler logic that only throttles the console, it does not try to terminate the server. In addition, this change has reduced the number of go-routines needed by the application and dramatically simplified internal logic.
* Removed the `--profiler` flag and replaced it with `--pprof` which will start an internal server listening on `localhost:6060` allowing you to use Go's standard `pprof` tooling.
* Replaced the `json` log driver for Docker containers with `local` to reduce the amount of overhead when it comes to streaming logs from instances.

## v1.5.6
### Fixed
* Rewrote handler logic for the power actions lock to hopefully address issues people have been having when a server crashes and they're unable to start it again until restarting Buddy.
* Fixes files uploaded with SFTP not being owned by the Pteranodon user.
* Fixes excessive memory usage when large lines are sent through the console event handler.

### Changed
* Replaced usage of `encoding/json` throughout the codebase with a more performant encoder (`goccy/go-json`) to hopefully improve overall performance for JSON operations.
* Added custom `ContainerInspect` function to handle direct calls to Docker's CLI and make use of the new JSON encoder logic. This should reduce the total number of memory allocations and be more performant overall in a hot pathway.

## v1.5.5
### Fixed
* Fixes sending to a closed channel when sending server logs over the websocket
* Fixes `buddy diagnostics` uploading no content
* Fixes a panic caused by the event bus closing channels multiple times when a server is deleted

## v1.5.4
### Fixed
* Fixes SSL paths being improperly converted to lowercase in environments where the path is case-sensitive.
* Fixes a memory leak due to the implemention of server event processing.

### Changed
* Selecting to redact information now redacts URLs from the log output when running the diagnostic command.

### Added
* Adds support for modifying the default memory overhead percentages in environments where the shipped values are not adequate.
* Adds support for sending the `Access-Control-Request-Private-Network` header in environments where Buddy will be accessed over a private network. This is defaulted to `off`.

## v1.5.3
### Fixed
* Fixes improper event registration and error handling during socket authentication that would cause the incorrect error message to be returned to the client, or no error in some scenarios. Event registration is now delayed until the socket is fully authenticated to ensure needless listeners are not registed.
* Fixes dollar signs always being evaluated as environment variables with no way to escape them. They can now be escaped as `$$` which will transform into a single dollar sign.

### Changed
* A websocket connection to a server will be closed by Buddy if there is a send error encountered and the client will be left to handle reconnections, rather than simply logging the error and continuing to listen for new events.

## v1.5.2
### Fixed
* Fixes servers not properly re-syncing with the Panel if they are already running causing them to be hard-stopped when terminated, rather than stopped with the configured action.

### Changed
* Changes SFTP server implementation to use ED25519 server keys rather than deprecated SHA1 RSA keys.

### Added
* Adds server uptime output in the stats event emitted to the websocket.

## v1.5.1
### Added
* Global configuration option for toggling server crash detection (`system.crash_detection.enabled`)
* RPM specfile

## v1.5.0
### Fixed
* Fixes a race condition when setting the application name in the console output for a server.
* Fixes a server being reinstalled causing the `file_denylist` parameter for an Egg to be ignored until Buddy is restarted.
* Fixes YAML file parser not correctly setting boolean values.
* Fixes potential issue where the underlying websocket connection is closed but the parent request context is not yet canceled causing a write over a closed connection.
* Fixes race condition when closing all active websocket connections when a server is deleted.
* Fixes logic to determine if a server's context is closed out and send a websocket close message to connected clients. Previously this fired off whenever the request itself was closed, and not when the server context was closed.

### Added
* Exposes `8080` in the default Docker setup to better support proxy tools.

### Changed
* Releases are now built using `Go 1.17` — the minimum version required to build Buddy remains `Go 1.16`.
* Simplifed the logic powering server updates to only pull information from the Panel rather than trying to accept updated values. All parts of Buddy needing the most up-to-date server details should call `Server#Sync()` to fetch the latest stored build information.
* `Installer#New()` no longer requires passing all of the server data as a byte slice, rather a new `Installer#ServerDetails` struct is exposed which can be passed and accepts a UUID and if the server should be started after the installer finishes.

### Removed
* Removes complicated (and unused) logic during the server installation process that was a hold-over from legacy Buddy architectures.
* Removes the `PATCH /api/servers/:server` endpoint — if you were previously using this API call it should be replaced with `POST /api/servers/:server/sync`.

## v1.4.7
### Fixed
* SFTP access is now properly denied if a server is suspended.
* Correctly uses `start_on_completion` and `crash_detection_enabled` for servers.

## v1.4.6
### Fixed
* Environment variable starting with the same prefix no longer get merged into a single environment variable value (skipping all but the first).
* The `start_on_completion` flag for server installs will now properly start the server.
* Fixes socket files unintentionally causing backups to be aborted.
* Files extracted from a backup now have their preior mode properly set on the restored files, rather than defaulting to 0644.
* Fixes logrotate issues due to a bad user configuration on some systems.

### Updated
* The minimum Go version required to compile Buddy is now `go1.16`.

### Deprecated
> Both of these deprecations will be removed in `Buddy@2.0.0`.

* The `Server.Id()` method has been deprecated in favor of `Server.ID()`.
* The `directory` field on the `/api/servers/:server/files/pull` endpoint is deprecated and should be updated to use `root` instead for consistency with other endpoints.

## v1.4.5
### Changed
* Upped the process limit for a container from `256` to `512` in order to address edge-cases for some games that spawn a lot of processes.

## v1.4.4
### Added
* **[security]** Adds support for limiting the total number of pids any one container can have active at once to prevent malicious users from impacting other instances on the same node.
* Server install containers now use the limits assigned to the server, or a globally defined minimum amount of memory and CPU rather than having unlimited resources.

## v1.4.3
This build was created to address `CVE-2021-33196` in `Go` which requires a new binary
be built on the latest `go1.15` version.

## v1.4.2
### Fixed
* Fixes the `~` character not being properly trimmed from container image names when creating a new server.

### Changed
* Implemented exponential backoff for S3 uploads when working with backups. This should resolve many issues with external S3 compatiable systems that sometimes return 5xx level errors that should be re-attempted automatically.
* Implements exponential backoff behavior for all API calls to the Panel that do not immediately return a 401, 403, or 429 error response. This should address fragiligty in some API calls and address random call failures due to connection drops or random DNS resolution errors.

## v1.4.1
### Fixed
* Fixes a bug that would cause the file unarchiving process to put all files in the base directory rather than the directory in which the files should be located.

## v1.4.0
### Fixed
* **[Breaking]** Fixes `/api/servers` and `/api/servers/:server` not properly returning all of the relevant server information and resource usage.
* Fixes Buddy improperly reading `BUDDY_UID` and not `BUDDY_GID` when running in containerized environments.
* Fixes a panic encountered when returning the contents of a file that is actively being written to by another process.
* Corrected the handling of files that are being decompressed to properly support `.rar` files.
* Fixes the error message returned when a server has run out of disk space to properly indicate such, rather than indicating that the file is a directory.

### Changed
* Improved the error handling and output when an error is encountered while pulling an image for a server.
* Improved robustness of code handling value replacement in configuration files to not potentially panic if a non-string value is encountered as the replacement type.
* Improves error handling throughout the server filesystem.

### Added
* Adds the ability to set the internal name of the application in response output from the console using the `app_name` key in the `config.yml` file.

## v1.3.2
### Fixed
* Correctly sets the internal state of the server as restoring when a restore is being performed to avoid any accidental booting.

## v1.3.1
### Fixed
* Fixes an error being returned to the client when attempting to restart a server when the container no longer exists on the machine.

### Changed
* Updated server transfer logic to use newer file archiving tools to avoid frequent errors when transferring symlinked files.

## v1.3.0
### Fixed
* Fixes improper error handling when attempting to create a new Docker network.
* Fixes edge-case crash that would occur when a user triggers an install for a server that does not currently have a data directory present on the system.
* Fixes missing return on error when attempting to get the contents of a file from Buddy.
* Fixes certain stop signals not being properly handled and parsed by Buddy.
* Fixes server build settings not always being updated properly if set to their zero-value.
* Fixes context leak when waiting on a server instance to be stopped.
* Fix potential application panic when chowning a file if there is an error getting file details.
* Fixes `Filesystem.Chown` unintentionally touching all of the files within a given directory tree which could cause some games to trigger a full refresh thinking files had been changed.
* Fixes `Content-Disposition` header not being properly escaped causing some browsers to not report the correct filename on downloads.

### Added
* Adds support for restoring server backups (including remote backups) with the ability to reset the current file state for a server.
* Adds underlying support for allowing Eggs to mark specific files (or patterns) as being inaccessible to users within the file manager.

### Changed
* Refactored SFTP subsystem to be less of a standalone package and more integrated with the underlying server logic in Buddy. This significantly simplified the logic and makes it much easier to reason about.
* Refactored much of the underlying API logic to be more extensible down the road, support automatic retries, and be more testable.
* Refactored much of the underlying HTTP middleware logic to be packaged differently and easier to reason about in the codebase.
* System defined `TZ` variable will be used if present rather than attempting to parse the timezone using `datetimectl`.
* Improves error handling and reporting for server installation process to improve debugging in the future if things break.

## v1.2.3
### Fixed
* **[Security]** Fixes a remaining security vulnerability in the code handling remote file downloads for servers relating to redirect validation.

### Added
* Adds a configuration key at `api.disable_remote_download` that can be set to `true` to completely download the remote download system.

## v1.2.2
### Fixed
* Reverts changes to logic handling blocking until a server process is done running when polling stats. This change exposed a bug in the underlying Docker system causing servers to enter a state in which Buddy was unable to terminate the process and Docker commands would hang if executed against the container.

### Changed
* Adds logic to handle a console stream unexpectedly returning an EOF when reading console logs. New code should automatically re-attach the stream avoiding issues where the console would stop live updating for servers.

## v1.2.1
### Fixed
* Fixes servers not be properly marked as no longer transfering if an error occurs during the archive process.
* Fixes problems with user detection when running Buddy inside a Docker container.
* Fixes filename decoding issues with multiple endpoints related to the file manager (namely move/copy/delete).
* **[Security]** Fixes vulnerability allowing a malicious user to abuse the remote file download utilitity to scan or access resources on the local network.
* Fixes network `tx` stats not correctly being reported (was previously reporting `rx` for both `rx` and `tx`).

### Changed
* Cleans up the logic related to polling resources for the server to make a little more sense and not do pointless `io.Copy()` operations.

## v1.2.0
### Fixed
* Fixes log compression being set on the Docker containers being created to avoid errors on some versions of Docker.
* Cleaned up logic handling server resource usage to avoid race conditions in the future and make the logic simpler.
* Fixes directories being created when writing a file before checking if there was space for the file to even be written to the disk.
* Significant performance and resource usage fixes to backups and server transfers to avoid obliterating machine `i/o` and causing excessive resource exhaustion on busy systems or low end machines.
* Fixes server install process to not unintentionally exit and cause invalid states if a line during the install process was too long.
* Fixes symlink error handling in backups to not unexpectedly tank a request. Any errors due to a symlink are now ignored and will not impact the generation of a backup (including for server transfers).

### Changed
* Changed `--debug` flag to no longer ignore certificate errors on requests. Use `--ignore-certificate-errors` to ignore any certificate errors encountered when in development environments.
* Changed all Filesystem related errors to be of the same internal error type making error checking significantly easier and less error prone.
* Improves log output stacktraces to be more accurate as to the source of the issue.

### Added
* Adds support for downloading files to a server's data directory and optionally checking the status of or canceling in-progress downloads.
* Adds a `context.Context` to `server.Server` structs allowing for cancelation of long running background tasks when a server is deleted without additional complexity on developer's end.

## v1.1.3
### Fixed
* Fixes `archive/tar: write too long` error when creating a server backup.
* Fixes server installation docker images not using authentication properly during the pull.
* Fixes temporary transfer files not being removed after the transfer is completed.
* Fixes TLS certificate checking to be all lowercase to avoid any lookup issues when an all-caps domain is provided.
* Fixes multiple interfaces with the same port not being publishable for a server.
* Fixes errors encountered during websocket processes being incorrectly passed back to the Panel as a JWT error rather than a generic Buddy error for admin users.

### Added
* Added logic to notify the Panel when archive generation fails.
* Added endpoint to run `chmod` commands against server files and updated API response to include the mode bits when requesting files.

### Changed
* Updated internals to call `Server.Environment.State()` rather than deprecated `Server.GetState()` functions.
* Improved error handling logic and massively simplified error passing around the codebase.

## v1.1.2
### Fixed
* Fixes binaries built as part of the release process not being usable in MUSL based environments (such as our Docker images).
* Fixes server states being incorrectly set back to offline when a server is started after a system restart.

### Changed
* Improved logic for cleaning `allowed_mount` paths for consistency.
* Certain context cancelation deadline errors are no longer wrong reported at an error level (since they're expected).
* Very minor micro-optimizations for some string handling with server console output.

### Added
* Added a hidden option to disable all disk checking for servers by setting the `disk_check_interval` to `0` in the config file.

## v1.1.1
### Fixed
* Fixes certain files returning invalid data in the request due to a bad header set after sending data down the line.

## v1.1.0
This release **requires** `Panel@1.1.0` or later to run due to API changes.

### Added
* Adds support for denying client JWT access to specific token keys generated before Buddy starts, or before an arbitrary date from an API call.
* Adds support for a configurable number of log messages to be returned when connecting to a server socket and requesting the logs.
* Adds support for both CPU and Memory profiling of Buddy via a CLI argument.

### Fixed
* Errors encountered while uploading files to Buddy are now properly reported back to the client rather than causing a generic 500 error.
* Servers exceeding their disk limit are now properly stopped when they exceed limits while running.
* Fixes server environment starting as an empty value rather than an "offline" value.

### Changed
* Cleaned up code internals for handling API requests to make it easier on new developers and use a more sane system.
* Server configuration retrieval from the Panel is now done in a paginated loop rather than a single large call to allow systems with thousands of instances to boot properly.
* Switches to multipart S3 uploads to handle backups larger than 5GB in size.
* Switches the error handling package from `pkg/errors` to `emperror` to avoid overwriting existing stack traces associated with an error and provide additional functionality.

## v1.0.1
### Added
* Adds support for ARM to build outputs for buddy.

### Fixed
* Fixed a few docker clients not having version negotiation enabled.
* Fixes local images prefixed with `~` getting pulled from remote sources rather than just using the local copy.
* Fixes console output breaking with certain games when excessive line length was output.
* Fixes an error when console lines were too long that would cause the console to stop updating until the server was restarted,

### Changed
* Simplified timezone logic for containers by properly grabbing the system timezone and then passing that through to containers with the `TZ=` environment variable.

## v1.0.0
This is the first official stable release of Buddy! Please be aware that while this specific version changelog is very short,
it technically includes all of the previous beta and alpha releases within it. For the sake of version history and following
along though, I've only included the differences between this version and the previous RC build.

### Fixed
* Fixes file parser not properly appending newline character to modified lines.
* Fixes server disk usage not being properly reported back to the API and websocket.

### Changed
* Changes diagnostics endpoint URL to be `ptero.co` rather than `hastebin.com`.
* Diagnostics report now includes system time for easier debugging of logs and container issues.

## v1.0.0-rc.7
### Fixed
* **[Security]** Prevents the `allowed_mounts` configuration value from being set by a remote API call.
* Fixes an unexpected error when attempting to make a copy of an archive file.
* Fixes certain expected filesystems errors being returned to the API as a 500 error rather than a 4XX series error.
* Fixes a panic crash when there is no text on a line and the parser is attempting to determine if the line is a comment.
* Fixes multiple filesystem operations to correctly check, increment, and decrement disk usage for a server.
* Fixes a negative disk space usage issue when deleting a file due to a bad int swap.
* Errors from a websocket connection being closed will no longer spam the console.
* Fixes an extraneous `.` in the install script for servers causing errors in some scenarios.
* Fixes unexpected error spam due to a change in how `os.ErrNotExist` is returned from some functions.

### Changed
* Stacktrace is no longer emitted when warning that an image exists locally.
* Configuration parser now attempts to create the directory structure leading to a configuration file if it is missing.
* If a file name is too long for the system a nice error is returned to the caller.
* Enables client version negotiation for Docker to support more versions.
* Disk space errors are no longer logged into the buddy logs.
* Servers can no longer be reinstalled while another power action is currently running. This avoids data collisions and buddy running into issues with the container state.
* Buddy now uses `1024` rather than `1000` bytes when calculating the disk space usage for a server to match how the Panel reports usage.
* JWT errors in the websocket are now sent back to the connection as a specific event type allowing them to be handled even if the timer fails to execute or executes but is not being listened for.
* A server struct is no longer embedded in the virtual filesystem allowing for easier testing and modularization of the codebase.
* Server websockets are now closed when a server is deleted, disconnecting any currently connected clients.

### Removed
* **[Security]** Removes the `SafeJoin` function which could wrongly assume the state of a file and allow a user to escape the root if the developer implementing the call calls `Stat` rather than `Lstat`.

## v1.0.0-rc.6
### Fixed
* Fixes race condition when checking if the running state of a server has changed.
* Fixes files mistakenly unpacking themselves to the root directory rather than the directory they are told to unpack to.
* Fixes console output not being sent to the websocket in the same order that it was received.
* Fixes a file busy error causing a 500 error response when unpacking an archive rather than a 400 error with a message indicating what is wrong to the user.
* Fixes docker image not properly updating when a server is started.

### Changed
* Replaces fragile event bus logic with a more robust and easier to understand system. This fixes all remaining console and stats output issues that have been reported.
* Cleans up API response error messaging to avoid empty errors being logged which cannot be acted upon.
* Adds support for retrying a file write a few times with a back-off if the file is busy when the write occurs.

### Added
* Docker image pull data is shown in the console when an admin is connected to the websocket.
* Adds console throttling to stop a server if too much data is being piped out of the console. This logic mimics the logic present in the old Nodejs daemon, but with a 2x line count limit (1000 -> 2000) per period.

## v1.0.0-rc.5
### Fixed
* Fixes long standing bug with console output not properly sending back to the client with server stats in certain edge case scenarios. This was "fixed" in `rc.4` but ended up breaking many servers in significantly more painful manners, but did allow us to better track down the issue.
* Fixes build flags during release process to correctly strip unnessary code allowing the final binary size to be reduced back down to `~5MB`
* Fixes Buddy returning the last `16384` lines of the log file when connecting to the websocket.
* Fixes pre-boot actions always running for a server start event even if the server is already running.

### Added
* Adds support for configuring the amount of time that can elapse between server disk size checking before the value is considered stale.

## v1.0.0-rc.4
### Fixed
* Fixes server files being inaccessible if the root data path is a symlink to another location on the machine.
* Fixes some console output being written to the logs accidentally truncating other lines due to special ANSI sequences being output.
* Fixes `server.properties` files getting mangled by the automatic configuration editor when booting a server.
* Fixes a missed stream close when stopping resource polling that would lead to memory leaks.
* Fixes port bindings being incorrectly re-assigned when using `127.0.0.1` with Docker. These are now properly re-mapped to the `pteranodon0` interface so that networking operates as expected for the server.
* Fixes handling of values within arrays in `yaml` and `json` configuration files.
* Fixes a 304 error being returned rather than a 400 error when a file upload is invalid.
* Fixes deadlocks and response delays when processing server console and stat events. This was causing server consoles to become completely unresponsive after random periods of time and was only resolvable via a Buddy restart.

### Changed
* Allows a stale value to be present when starting a server if the server is allowed an unlimited amount of disk space.
* Removed all remaining traces of the `zap` logging library from the codebase.
* Servers no longer auto-restart as if they crashed when a stop command is manually sent via the server console to the instance.
* Changes the CORS checks to allow `*` as a remote origin.

### Added
* Added an auto-generated logrotate file which is written to the normal logrotate directory when buddy is first started.
* Added additional debug logging within the internals of the resource polling for servers to better trace unexpected behavior.
* Adds additional logic check to avoid even trying to stop a suspended server if it is already stopped.

## v1.0.0-rc.3
### Fixed
* Errors during the backup process are now correctly reported to the Panel and logged into the output correctly.
* Empty directories are no longer pushed into the backup file list (which was causing errors to occur previously).
* Covered an edge case to prevent errors if a file gets deleted while a backup is in progress.
* Fixed a bug causing Buddy to panic and crash if an invalid environment variable value was passed through. These invalid values are now logged to the output to better detect and an empty string is returned in their place.
* Fixed startup variables and other server information not properly being updated when a server is restarted.
* Suspension state of a server is now properly returned by the API.
* Fixes an error being thrown if a backup does not exist on the local machine. A 404 is now properly returned and handled by the Panel when this occurs.
* Fixes an error when attempting to move or rename a folder due to the target being created accidentally before the rename occurred.
* Fixes install scripts running even when the checkbox to not run them on installation was selected in the Panel.

### Changed
* Disk space checking modified to not block in as many parts of the codebase and allow returning a stale cache value where that is appropriate. Thanks @cyberkitsune!
* SFTP package code is now merged into the codebase to make keeping up with all of the code simpler, and reduce the complexity of the SFTP server.

### Added
* Added the ability for a node to be configured to skip file permissions checking when starting a server.
* Added console output message to indicate that server disk space checking is occurring rather than silently sitting there.

## v1.0.0-rc.2
### Fixed
* Fixes significant performance degradation due to excessive `syscall` actions when determining directory sizes on large servers. This was previously causing CPU & I/O lockups on servers and should be significantly more performant and less impactful on the system now.
* Fixes panic crash when booting buddy with no log directory created.

### Changed
* Changed default interval for disk space calculation from every 60 seconds to every 2.5 minutes.

## v1.0.0-rc.1
### Fixed
* Servers are no longer incorrectly marked as stopping when they are, in fact, offline.
* Release build version is now correctly output when starting buddy.
* Termination signals can now always be sent to a server instance even if the instance is currently starting/stopping.
* Removed the file chown on buddy boot to avoid slowing down the boot process unnecessarily when working with hundreds of servers on a node.
* Fixed a multitude of race conditions throughout the code that cropped up during testing and made power handling even more robust for server instances.
* In general multiple fragile areas of the codebase have been improved and are more likely to return useful errors should they break.
* Addressed serious CPU usage issues when generating backups, and additionally decreased the amount of time they take to generate.

### Changed
* Internal server now uses more secure and recommended TLS settings.
* Environment handling is now completely separate from the server package itself, allowing the environments to no longer be tightly coupled to the server.
* `/tmp` directory mounted into containers can now be programmatically managed and uses better defaults to avoid people even needing to edit it.

### Added
* Buddy logs are now properly persisted to the disk.
* Adds the ability for an egg to use ANSI-stripped matching when determining if a server is done booting.

## v1.0.0-beta.9
### Fixed
* Fixes server resource usage stats not being returned correctly by the API endpoint.
* Fixes an exception thrown when attempting to write server installation logs.
* Fixes error handling to provide a more accurate stack-trace in more scenarios where one is missing initially.
* Fixes a memory leak and zombie event listeners when disconnecting from a server's websocket.
* Fixes a race condition when buddy is attempting to register/de-register event subscribers.
* Server data directories now correctly have their permissions set recursively when booting Buddy.
* Fixes a race condition when a server's console stream was not fully closed before the next power action was started.

### Changed
* Server power handling is now handled in a synchronous manner. This avoids endless bugs and race conditions that would crop up if someone triggered two restart processes back to back. The new logic prevents performing any additional power actions until the currently executing action is completed.
* Server disk usage is now correctly calculated when restarting the daemon as long as the server data directory exists.
* Multiple code pathways within the boot process were cleaned up and modified to be less IO intensive and overall easier to reason about as a developer.
* Additional timezone data is now mounted into containers to better improve the ability for instances running to be using the correct timezone.

### Added
* Adds basic internal file upload endpoints (these are currently not exposed anywhere in the Panel).
* Added additional process events for installation start and completion.
* Additional CORS allowed origins for the websocket can now be defined in the configuration file.
* Adds the ability to authenticate with a docker registry when pulling images.

## v1.0.0-beta.8
### Fixed
* Server state is synced with the Panel before performing a reinstall to ensure that the latest information is used.
* Buddy no longer crashes when a non-string environment variable value is passed through.
* SFTP server authentication no longer attempts to contact the Panel to validate credentials if the format is known to already be incorrect.
* Some previously missing error stacks are now properly returned when encountered.
* Renaming a file no longer triggers an error if the base path does not exist.
* Disk space is now properly calculated for a server even if it is assigned unlimited space.
* **[Security]** Prevent symlink files from unintentionally chmodding their target file when a server is booted when the target file resides outside the server data directory.
* **[Security]** Cleans up multiple code pathways that may unintentionally allow a malicious user to impact files outside their home directory.
* Fixes numerous race conditions during a server's boot process that may lead to unintended data states.
* Fixes an error thrown when attempting to delete a file that points to a symlink outside the server data directory.
* Deleting a symlink will no longer attempt to remove the source file, only the symlink itself.
* Websocket no longer blocks when handling a long running process for a user.

### Changed
* Install log for servers is now more detailed and more useful for debugging what might be going wrong.
* Certain file management API endpoints now support passing through multiple paths at once to make mass actions easier to perform.
* Re-worked file walking implementation to not cause a server crash when working with very large directories, and avoid race conditions when recursively walking directories.
* Server configuration structure re-worked to be more manageable in the code base and avoid additional race conditions and complexity while making on-the-fly changes to it.

### Added
* Support for configuring additional file mount points in a container via the Panel.
* Support for automated SSL certificate generation when booting the Daemon.
* Added buddy diagnostics command.
* New API endpoints to compress and decompress files on a server.

## v1.0.0-beta.7
### Fixed
* Stacktraces are now displayed once in the error output, rather than twice when certain errors are encountered.
* Additional errors that did not previously output a stack trace have been fixed to output.
* Fixes a bug where server space available would be triggered when creating a new server from a remote configuration before that file location existed on the disk, leading to an error.
* Fixes context timeouts while pulling server docker images. Time was upped from 10 seconds to 15 minutes.
* Configuration file replacement values are now properly de-escaped when writing to the disk. `\/no\/more\/slashes`
* `.properties` files are now saved correctly to the disk with newlines, rather than shoved on a single line.
* `./buddy configure` command can now properly save the configuration to the disk.
* Custom SSL locations are no longer obliterated when making changes to the Node's configuration via the Panel.

### Changed
* Exclusive lock is now acquired when performing a server installation to avoid two install processes being triggered at the same time. This also allows an install to be properly canceled if the server is deleted before it is completed.

## v1.0.0-beta.6
### Fixed
* Server status is no longer sent to all connected websocket clients when a new client connects to the socket.
* Server disk usage is sent back over the socket when connecting.
* Default configuration for the SFTP server is now properly returned to `on` rather than `off`
* Server boot is no longer blocked if there is an error fetching the Docker image as long as that image exists on the host.
* The websocket no longer gets locked up when Buddy attempts to send an error to the client.
* Fixed a crash loop when an error is thrown during pre-boot phase of server startup.
* Errors with `BindJSON` in API endpoints are now properly handled and returned.
* Fixed warning about Gin running in non-release mode, even when the binary is running in release mode.

### Changed
* Switched logging libraries to output data in a clearer format more suited to the CLI where this application runs.
* Cleaner debug messaging in debug mode from the router.

## v1.0.0-beta.5
### Fixed
* Default config location settled on `/etc/pteranodon/config.yml`; buddy will now check all of the previous locations for the configuration and move it automatically to the new location.
* Deleting a server no longer fails the process if the container cannot be found.
* Fixes permissions checking for subusers connecting to the SFTP instance.
* S3 backups now properly send back hash data to the panel.
* Server installation containers are now always deleted, even if the installer process fails for the instance.
* Files and folders with special characters and spaces in them no longer return a 404 error.
* Servers using eggs with bad configurations will no longer cause the daemon to fail booting; these bad configurations are simply skipped over and a warning is emitted into the logs.
* Environment variables passed to containers no longer contain improper quotes around them.
* Matching on array indexes in configurations now works correctly; `foo[0]` is quietly transformed into the proper `foo.0` syntax.

### Added
* New banner error message when the daemon is unable to locate the configuration file. This should better clarify what the user needs to do in order to resolve the issue.
* Adds ability to configure the default networking driver used by docker.

## v1.0.0-beta.4
### Fixed
* Fixes unexpected nil-pointer panic when attempting to start some newly created servers, or any server that was missing a container on the system.
* Fixes memory usage of process being reported differently than the `docker stats` output leading to some confusion. These numbers should be more correct now.
* Fixes possible nil-pointer panic when detecting a deleted container as being crashed.

## v1.0.0-beta.3
### Fixed
* Daemon will no longer crash if someone requests a websocket for a deleted server.
* Temporary directories are now created properly if missing during the server installation process.

### Added
* Added support for using Amazon S3 as a backup location for archives.

### Changed
* Memory overhead for containers is now 5/10/15% higher than the passed limit to account for JVM heap and prevent crashing.

## v1.0.0-beta.2
### Changed
* Backup functionality made significantly more modular to ease adding additional methods in the future.
* Websocket permissions changed to use same name as in panel.
* Container memory hard-limits are now adjusted up by 15% (< 2G memory), 10% (< 4G memory), or 5% to avoid unexpected OOM crashes for memory heavy games.
* Buddy executable is now 80% smaller thanks to better compilation arguments.

### Added
* Adds support for ignoring files and directories when generating a new backup.
* Added internal directory walker with callback-continue support.

### Fixed
* Fixed linux distro matching when booting the daemon.
* Fixes DNS to be configurable for docker containers that are made for servers.
* Fixes incorrect file truncation when making modifications to a server's configuration files.

## v1.0.0-beta.1
### Added
* Added support for passing specific threads to the docker environment when running a server.
* Added support for reinstalling an existing server.
* Added support for restarting a server.
* Adds support for transferring servers between daemon instances.
* Added auto-deploy command to fetch configuration from Panel automatically.

### Changed
* Server file and backup downloads now hit a direct endpoint with a one-time JWT to avoid proxying large downloads through the panel.

### Fixed
* Fixes a go routine causing a memory and CPU leak.
* Fixed improper chown of server directories on boot.
* Miscellaneous fixes to CPU usage, abandoned go-routines, and race conditions.
* Fixes support for Alpine based systems.

## v1.0.0-alpha.2
### Added
* Ability to run an installation process for a server and notify the panel when completed.
* Output from the installation process is now emitted over the server console for administrative users to view.

### Fixed
* Fixed bugs caused when unmarshaling data into a struct with default values.
* Timezone is properly set in containers by using the `TZ=` environment variable rather than mounting timezone data into the filesystem.
* Server data directly is now properly created when running the install process.
* Time drift caused in the socket JWT is now handled in a better manner and less likely to unexpectedly error out.
